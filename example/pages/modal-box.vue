<template>
  <div class="page-msgbox">
    <h1 class="page-title">Message Box</h1>
    <div class="page-msgbox-wrapper">
      <mt-button @click.native="openAlert" size="large">打开 alert 提示框</mt-button>
      <mt-button @click.native="openAlertNoActionButton" size="large">打开 confirm 提示框有action button</mt-button>
      <mt-button @click.native="openAlertHasActionButton" size="large">打开 confirm 提示框没有action button</mt-button>
      <mt-button @click.native="openConfirm" size="large">打开 confirm 提示框</mt-button>
      <mt-button @click.native="openPrompt" size="large">打开 prompt 提示框</mt-button>
    </div>
  </div>
</template>

<style>
  @component-namespace page {
    @component msgbox {
      @descendent wrapper {
        padding: 0 20px;
        position: absolute 50% * * *;
        width: 100%;
        transform: translateY(-50%);
        button:not(:last-child) {
          margin-bottom: 20px;
        }
      }
    }
  }
  .mint-msgbox-wrapper .dddd {
    width: 10rem;
  }
</style>

<script type="text/babel">
  import { ModalBox } from 'src/index';
  import slotModal from './../test_component/slot_modal';
  import Vue from 'vue';

  export default {
    methods: {
      openAlert() {
        const h = this.$createElement;
        ModalBox.confirm(h(slotModal), '', { 
          confirmButtonHighlight: true, 
          showButton: true,
          showClose: true, 
          showCancelButton: true,
          customClass : "dddd"
        }).then(res => {
          console.log(res);
        },err => {
          console.log(err);
        });
      },
      openAlertNoActionButton() {
        const h = this.$createElement;
        ModalBox.confirm(h(slotModal), '', { 
          confirmButtonHighlight: true, 
          showButton: false,
          showClose: true, 
          showCancelButton: true
        }).then(res => {
          console.log(res);
        },err => {
          console.log(err);
        });
      },

      openAlertHasActionButton() {
        const h = this.$createElement;
        ModalBox.confirm(h(slotModal), '', { 
          confirmButtonHighlight: true, 
          showClose: true, 
          showCancelButton: true
        }).then(res => {
          console.log(res);
        },err => {
          console.log(err);
        });
      },

      openConfirm() {
        ModalBox.confirm('确定执行此操作?', '提示');
      },

      openPrompt() {
        ModalBox.prompt(' ', '请输入姓名').then(({ value }) => {
          if (value) {
            ModalBox.alert(`你的名字是 ${ value }`, '输入成功');
          }
        });
      }
    }
  };
</script>
