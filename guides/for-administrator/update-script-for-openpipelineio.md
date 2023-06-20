---
description: How to install update script for OpenPipelineIO
---

# Update script for OpenPipelineIO

업데이트에 필요한 파일을 생성합니다.

```
vim /usr/local/OpenPipelineIO/scripts/update_OpenPipelineIO.sh
```

자동으로 최신 OpenPipelineIO를 받아서 업데이트하고 서비스를 재시작합니다.

```
#!/usr/bin/sh
org=lazypic
app=OpenPipelineIO
systemctl stop OpenPipelineIO
version=`curl --silent "https://api.github.com/repos/$org/$app/releases/latest" | grep '"tag_name":' | sed -E 's/.*"([^"]+)".*/\1/'`
filename="${app}_linux_x86-64.tgz"
exepath=/usr/local/OpenPipelineIO/bin
# 다운로드 주소를 만든다.
url=https://github.com/$org/$app/releases/download/$version/$filename
mkdir -p $exepath
cd $exepath
rm $filename # 하위경로 제거하기
rm $app
wget $url # 다운로드
tar -zxvf $filename # 압축풀기
chmod 775 $app
rm $filename
systemctl start OpenPipelineIO
```

{% hint style="info" %}
**Good to know:** your product docs aren't just a reference of all your features! use them to encourage folks to perform certain actions and discover the value in your product.
{% endhint %}
