<template>
  <div class="mint-msgbox-wrapper">
    <transition name="msgbox-bounce">
      <div class="mint-msgbox" v-show="value" :class="customClass" @click.self="handleWrapperClick">
        <span class="close-btn" @click="handleAction('cancel')" v-if="showClose"></span>
        <div class="mint-msgbox-header">
          <div class="mint-msgbox-title" v-if="title !== ''">{{ title }}</div>
        </div>
        <div class="mint-msgbox-content" v-if="message !== ''">
          <div>
            <slot>{{ message }}</slot>
          </div>
          <div class="mint-msgbox-input" v-show="showInput">
            <input v-model="inputValue" :placeholder="inputPlaceholder" ref="input">
            <div class="mint-msgbox-errormsg" :style="{ visibility: !!editorErrorMessage ? 'visible' : 'hidden' }">{{ editorErrorMessage }}</div>
          </div>
        </div>
        <div class="mint-msgbox-btns" v-show="showButton">
          <button :class="[ cancelButtonClasses ]" v-show="showCancelButton" @click="handleAction('cancel')">{{ cancelButtonText }}</button>
          <button :class="[ confirmButtonClasses ]" v-show="showConfirmButton" @click="handleAction('confirm')">{{ confirmButtonText }}</button>
        </div>
      </div>
    </transition>
  </div>
</template>

<style>
  @component-namespace mint {
    @component msgbox {
      position: fixed;
      top: 50%;
      left: 50%;
      transform: translate3d(-50%, -50%, 0);
      background-color: #fff;
      width: 85%;
      border-radius: 3px;
      font-size: 16px;
      -webkit-user-select: none;
      overflow: hidden;
      backface-visibility: hidden;
      transition: .2s;
      
      & span.close-btn {
        position: absolute;
        width: 20px;
        height: 20px;
        top: 10px;
        right: 10px;
        z-index: 10;
        &:before {
          position: absolute;
          top: 9px;
          content: "";
          display: block;
          width: 20px;
          height: 1px;
          background-color: #dcb15b; 
          line-height: 0;
          font-size:0;
          vertical-align: middle;
          -webkit-transform: rotate(45deg);
        };

        &:after {
          content: "";
          top: 9px;
          background-color: #dcb15b;
          position: absolute;
          display:block;
          width: 20px;
          height: 1px;
          -webkit-transform: rotate(-45deg);
        };
      }

      @descendent header {
        padding: 15px 0 0;
      }

      @descendent content {
        min-height: 36px;
        position: relative;
      }

      @descendent input {
        padding-top: 15px;
        & input {
          border: 1px solid #dedede;
          border-radius: 5px;
          padding: 4px 5px;
          width: 100%;
          appearance: none;
          outline: none;
        }
        & input.invalid {
          border-color: #ff4949;
          &:focus {
            border-color: #ff4949;
          }
        }
      }

      @descendent errormsg {
        color: red;
        font-size: 12px;
        min-height: 18px;
        margin-top: 2px;
      }

      @descendent title {
        text-align: center;
        padding-left: 0;
        margin-bottom: 0;
        font-size: 16px;
        font-weight: bold;
        color: #333;
      }

      @descendent btns {
        display: -webkit-box;
        display: -webkit-flex;
        display: -ms-flexbox;
        display: flex;
        border-top: 1px solid #ddd;
        height: 40px;
        line-height: 40px;
      }

      @descendent btn {
        line-height: 35px;
        display: block;
        background-color: #fff;
        flex: 1;
        margin: 0;
        border: 0;

        &:focus {
          outline: none;
        }

        &:active {
          background-color: #fff;
        }
      }

      @descendent cancel {
        width: 50%;
        border-right: 1px solid #ddd;
        &:active {
          color: #000;
        }
      }

      @descendent confirm {
        color: #26a2ff;
        width: 50%;
        &:active {
         color: #26a2ff;
        }
      }
    }
  }

  .msgbox-bounce-enter {
    opacity: 0;
    transform: translate3d(-50%, -50%, 0) scale(0.7);
  }
  .msgbox-bounce-leave-active {
    opacity: 0;
    transform: translate3d(-50%, -50%, 0) scale(0.9);
  }
</style>
<style src="mint-ui/src/style/popup.css"></style>

<script type="text/babel">
  let CONFIRM_TEXT = '确定';
  let CANCEL_TEXT = '取消';

  import Popup from 'mint-ui/src/utils/popup';

  export default {
    mixins: [ Popup ],

    props: {
      modal: {
        default: true
      },
      showClose: {
        type: Boolean,
        default: true
      },
      lockScroll: {
        type: Boolean,
        default: false
      },
      closeOnClickModal: {
        default: true
      },
      closeOnPressEscape: {
        default: true
      },
      inputType: {
        type: String,
        default: 'text'
      }
    },

    computed: {
      confirmButtonClasses() {
        let classes = 'mint-msgbox-btn mint-msgbox-confirm ' + this.confirmButtonClass;
        if (this.confirmButtonHighlight) {
          classes += ' mint-msgbox-confirm-highlight';
        }
        return classes;
      },
      cancelButtonClasses() {
        let classes = 'mint-msgbox-btn mint-msgbox-cancel ' + this.cancelButtonClass;
        if (this.cancelButtonHighlight) {
          classes += ' mint-msgbox-cancel-highlight';
        }
        return classes;
      }
    },

    methods: {
      doClose() {
        if (!this.visible) return;
        this.value = false;
        this._closing = true;
        this.onClose && this.onClose();
        document.body.style.overflow = this.bodyOverflow;
        if (!this.transition) {
          this.doAfterClose();
        }
        if (this.action) this.callback(this.action, this);
      },

      handleWrapperClick() {
        if (this.closeOnClickModal) {
          this.handleAction('cancel');
        }
      },

      handleAction(action) {
        if (this.$type === 'prompt' && action === 'confirm' && !this.validate()) {
          return;
        }
        this.action = action;
        if (typeof this.beforeClose === 'function') {
          this.beforeClose(action, this, this.doClose);
        } else {
          this.doClose();
        }
      },

      validate() {
        if (this.$type === 'prompt') {
          var inputPattern = this.inputPattern;
          if (inputPattern && !inputPattern.test(this.inputValue || '')) {
            this.editorErrorMessage = this.inputErrorMessage || '输入的数据不合法!';
            this.$refs.input.classList.add('invalid');
            return false;
          }
          var inputValidator = this.inputValidator;
          if (typeof inputValidator === 'function') {
            var validateResult = inputValidator(this.inputValue);
            if (validateResult === false) {
              this.editorErrorMessage = this.inputErrorMessage || '输入的数据不合法!';
              this.$refs.input.classList.add('invalid');
              return false;
            }
            if (typeof validateResult === 'string') {
              this.editorErrorMessage = validateResult;
              return false;
            }
          }
        }
        this.editorErrorMessage = '';
        this.$refs.input.classList.remove('invalid');
        return true;
      },

      handleInputType(val) {
        if (val === 'range' || !this.$refs.input) return;
        this.$refs.input.type = val;
      }
    },

    watch: {
      inputValue() {
        if (this.$type === 'prompt') {
          this.validate();
        }
      },

      value(val) {
        this.handleInputType(this.inputType);
        if (val && this.$type === 'prompt') {
          setTimeout(() => {
            if (this.$refs.input) {
              this.$refs.input.focus();
            }
          }, 500);
        }
      },

      inputType(val) {
        this.handleInputType(val);
      }
    },

    data() {
      return {
        title: '',
        message: '',
        type: '',
        customClass: '',
        showInput: false,
        inputValue: null,
        inputPlaceholder: '',
        inputPattern: null,
        inputValidator: null,
        inputErrorMessage: '',
        showConfirmButton: true,
        showCancelButton: false,
        confirmButtonText: CONFIRM_TEXT,
        cancelButtonText: CANCEL_TEXT,
        confirmButtonHighlight: false,
        cancelButtonHighlight: false,
        confirmButtonClass: '',
        confirmButtonDisabled: false,
        cancelButtonClass: '',
        editorErrorMessage: null,
        callback: null,
        showButton: true
      };
    }
  };
</script>
