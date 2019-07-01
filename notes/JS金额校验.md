## 分享一个js的金额校验方法，适用于用户自己输入金额支付的场景

html代码
```html
	<input onkeyup='clearNoNum(this)' type='number' step='0.01' id='moneyNum' placeholder='输入金额'>
```
js代码
```js
function clearNoNum(obj) {
	    //先把非数字的都替换掉，除了数字和.
	    var vv = $("#moneyNum").val();
	    if((vv.indexOf(".")==-1) && vv.length>0){
	    	return;
	    }
	    
	    vv = vv.replace(/[^\d.]/g,"");
	    //必须保证第一个为数字而不是.
	    vv = vv.replace(/^\./g,"");
	    //保证只有出现一个.而没有多个.
	    vv = vv.replace(/\.{2,}/g,".");
	    //保证.只出现一次，而不能出现两次以上
	    vv = vv.replace(".","$#$").replace(/\./g,"").replace("$#$",".");
	    //小数点后保留两位
	    var pos = vv.indexOf(".");
	    //alert(pos);
	    if(pos>-1){
	        var pos1 = vv.substring(pos);
	       // alert(pos1);
	        if(pos1.length > 3){
	            vv = vv.substring(0, pos+3);
	        }
	    }
	   // alert(vv);
	    $("#moneyNum").val(vv);
	}
```
效果图
![在这里插入图片描述](https://img-blog.csdn.net/20181019112833755?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsYW5raW5ndjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

