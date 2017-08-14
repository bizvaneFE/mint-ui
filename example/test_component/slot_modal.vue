<template>
	<div>
		<span @click="openAlert">modal again</span>
		<span @click="cancel">取消</span>
		<span @click="confirm">确定</span>
	</div>
</template>
<script type="text/javascript">
	import { ModalBox } from 'src/index';
	export default {
		name: "modalSlot",
		data() {
			return {
				name: "modalSlot",
				formModel : {
					name: "zhangsan",
					age: 18
				}
			}
		},
		methods : {
			cancel() {
				this.$parent.handleAction('cancel');
			},
			confirm () {
				this.$parent.handleAction('confirm');
			},
			openAlert() {
		        const h = this.$createElement;
		        ModalBox.confirm('123', '', { 
		          confirmButtonHighlight: true, 
		          showButton: true,
		          showClose: true, 
		          showCancelButton: true
		        }).then(res => {
		          console.log(res);
		        },err => {
		          console.log(err);
		        });
		    }
		}
	}
</script>
<style type="text/css">
	
</style>