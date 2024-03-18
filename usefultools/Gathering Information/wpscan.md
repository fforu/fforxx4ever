**wpscanapi**: tYtl343tIjp9AjUaIHnLdZHA3QaRestXTHc7aBI59Jw

基础命令： wpscan –url IP_ADDRESS_OF_WEBSITE --api-token

枚举：-e u:（枚举网站用户）
    命令： wpscan –url IP_ADDRESS_OF_WEBSITE -e u

暴力破解用户密码：-U -P：（已识别用户的暴力破解密码）
    命令： wpscan –url IP_ADDRESS_OF_WEBSITE -U 用户名 -P PATH_TO_WORDLIST