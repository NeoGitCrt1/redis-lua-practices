# redis-lua-practices
```
redis-cli.exe -h 119.3.134.54 -n 3 --eval D:\tmp\reduslua.lua
// -n x 表示使用x的db
```
从指定pattern的key查找指定位置的字符串
```
local pattern = 'KCKHT:WXLOGIN:SSID:*'
local wxuserid = '17'
local a = redis.call('scan', '0', 'MATCH', pattern, 'COUNT', '10')
local b = ''
repeat
   for k, key in pairs(a[2]) do
		local str = redis.call('get', key)
		local start = 0
		local ends = 0
		for i=string.len(str),0,-1 do
			local first_sub = string.sub(str, i, i)
			if (first_sub == "}" ) 
			then
				ends = i
			elseif(first_sub == ":")
			then
				start = i
				break
			end	
		end
		if (string.sub(str, start+1, ends -1) == wxuserid) 
		then
			return str
		end
	end
	
   a = redis.call('scan', a[1], 'MATCH', pattern, 'COUNT', '10')
until( a[1] == [[0]]  )
return b

```
