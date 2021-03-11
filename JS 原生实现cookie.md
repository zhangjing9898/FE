原生cookie

### setCooie
```
function setCookie(name,value,iDay)
		{
			var oDate=new Date();
                       oDate.setDate(oDate.getDate()+iDay);
			document.cookie=name+'='+value+';expires='+oDate;
		};
setCookie('user',oUser.value,14);
```
setDate() 方法用于设置一个月的某一天。
返回:
```
Sun Apr 15 2018 13:45:21 GMT+0800 (中国标准时间)
```
### getCookie
```
function getCookie(name)
		{
			var arr=document.cookie.split('; ');
			
			for(var i=0;i<arr.length;i++)
			{
				arr2=arr[i].split('=');
				
				if(arr2[0]==name)
				{
					return arr2[1];
				}
				return ' ';
			}
		};
oUser.value=getCookie('user');
```

### clear
```
function removeCooki(name)
		{
			setCookie(name,1,-1);
		}
```
