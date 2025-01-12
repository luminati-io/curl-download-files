# The cURL Guide on Downloading Files

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/blob/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/) 

In this guide, you will see:

- [The basic cURL download file syntax](#basic-curl-download-file-syntax)
- [How to handle more complex scenarios when downloading files with cURL](#using-curl-to-download-a-file-advanced-options)
- [How to download multiple files at once](#how-to-download-multiple-files-with-curl)
- [Some best practices for using cURL effectively](#best-practices-when-downloading-files-with-curl)
- [A quick comparison between cURL and Wget](#curl-vs-wget-for-downloading-files)

Let’s dive in!

---

## Basic cURL Download File Syntax

Below is the most basic cURL download file syntax:

```powershell
curl -O <file_url>
```

> 💡 **Important:**
> On Windows, `curl` is an alias for [`Invoke-WebRequest`](https://github.com/luminati-io/Invoke-web-request-proxy) in Windows PowerShell. To avoid the conflict, replace `curl` with `curl.exe`.

The [`-O`](https://curl.se/docs/manpage.html#-O) and `--remote-name` flags tell cURL to save the downloaded file with its original name:

```powershell
curl --remote-name <file_url>
```  
For example, consider the following download file cURL command:

```powershell
curl -O "https://i.imgur.com/CSRiAeN.jpg"
```

This will produce an output with a download progress bar as below:

```
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 35354  100 35354    0     0   155k      0 --:--:-- --:--:-- --:--:--  158k
```

When the progress bar reaches 100%, a file named `CSRiAeN.jpg` will appear in the folder where you ran the cURL command:

![The downloaded CSRiAeN.jpg file in the folder where cURL was launched](https://github.com/luminati-io/curl-download-files/blob/main/image-37.png)

For more information on cURL and its options, [read our cURL guide](https://brightdata.com/blog/web-data/what-is-curl).

## Using cURL to Download a File: Advanced Options

Let's learn some additional options.

### Change Downloaded File Name

By default, the `-O` option saves the dowloaded file under its original name. If the remote file in the URL does not include a name, cURL creates a file with no extension called `curl_response`:

![The default curl_response file in the folder where cURL was launched](https://github.com/luminati-io/curl-download-files/blob/main/image-38.png)

cURL will also print a warning to inform you of that behavior:

```
Warning: No remote file name, uses "curl_response"
```

To specify a custom name for the downloaded file, use the [`-o`](https://curl.se/docs/manpage.html#-o) (or `--output`) flag:

```powershell
curl "https://i.imgur.com/CSRiAeN.jpg" -o "logo.jpg"
```

cURL will perform a GET request to the specified file URL and save the downloaded content under the name specified after `-o`. This time, the output file will be a `logo.jpg` file:

![The downloaded logo.jpg file in the folder where cURL was launched](https://github.com/luminati-io/curl-download-files/blob/main/image-39.png)

### Follow Redirects

Some URLs do not directly point to the desired file and require automatic redirects to reach the final destination.

To instruct cURL to follow redirects, use the [`-L`](https://curl.se/docs/manpage.html#-L) option:

```powershell
curl -O -L "<file_url>"
```

Otherwise, cURL will output the redirection response headers and not follow the new location provided in the `Location` header.

### Authenticate to the Server

Some servers restrict access and require user authentication. To perform basic HTTP or FTP authentication, use the [`-u`](https://curl.se/docs/manpage.html#-u) (or `--user`) option and specify a username and password in the following format:

```powershell
<username>:<password>
```

The format makes it impossible to include a colon in the username. However, the password can contain a colon.

The `<password>` string is optional. If you only specify the username, cURL will prompt you to enter the password.

Here is the syntax for downloading a file with cURL using server authentication:

```powershell
curl -O -u <username>:<password> <file_url>
```

For example, you can download a file from a URL with authentication using this command:

```powershell
curl -O -u "myUser:myPassword" "https://example.com/secret.txt"
```

### Impose Bandwidth Restrictions

To avoid using the full available bandwidth, use the [`--limit-rate`](https://curl.se/docs/manpage.html#--limit-rate) option followed by the maximum download speed you want to set:

```powershell
curl -O --limit-rate 5k "https://i.imgur.com/CSRiAeN.jpg"
```
The output will be similar to this:

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 35354  100 35354    0     0   5166      0  0:00:06  0:00:06 --:--:--  5198
```

The `--limit-rate` option helps control bandwidth usage to avoid overloading the network, comply with bandwidth restrictions, or simulate slower network conditions for testing purposes.

### Download Through a Proxy Server

To maintain privacy or avoid [anti-bot measures like rate limiting](https://brightdata.com/blog/web-data/anti-scraping-techniques), mask your IP and route your request through a proxy using the [`-x`](https://curl.se/docs/manpage.html#-x) (or `--proxy`) option:

```powershell
curl -x <proxy_url> -O <file_url>
```

`<proxy_url>` must be specified in the following format:

```powershell
[protocol://]host[:port]
```

The proxy URL will vary depending on whether you are using an HTTP, HTTPS, or SOCKS proxy. For more detailed instructions, refer to our [cURL proxy integration guide](https://brightdata.com/blog/proxy-101/curl-with-proxies).

For example, if you are using an HTTP proxy, the command would become:

```powershell
curl -x "http://proxy.example.com:8080" -O "https://i.imgur.com/CSRiAeN.jpg"
```

### Perform Background Downloads

To disable the progress bar and error messages in the output, enable the “quiet” mode using the [`-s`](https://curl.se/docs/manpage.html#-s) (or `--silent`) option:

```powershell
curl -O -s "https://i.imgur.com/CSRiAeN.jpg"
```

If the download is successful, the file will appear in the current directory without feedback in the terminal.

### Print Verbose Detail Information

In case of an error or to better understand what cURL is doing behind the scenes, use the verbose mode by appending the [`-v`](https://curl.se/docs/manpage.html#-v) (or `--verbose`) option:

```powershell
curl -O -v "https://i.imgur.com/CSRiAeN.jpg"
```

This will turn on additional output with detailed information:

```
* IPv6: (none)
* IPv4: 146.75.52.193
*   Trying 146.75.52.193:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* ALPN: server accepted http/1.1
* Connected to i.imgur.com (146.75.52.193) port 443
* using HTTP/1.x
> GET /CSRiAeN.jpg HTTP/1.1
> Host: i.imgur.com
> User-Agent: curl/8.10.1
> Accept: */*
>
* Request completely sent off
* schannel: failed to decrypt data, need more data
< HTTP/1.1 200 OK
< Connection: keep-alive
< Content-Length: 35354
< Content-Type: image/jpeg
< Last-Modified: Wed, 08 Jan 2025 08:02:49 GMT
< ETag: "117b93e0521ba1313429bad28b3befc8"
< x-amz-server-side-encryption: AES256
< X-Amz-Cf-Pop: IAD89-P1
< X-Amz-Cf-Id: wTQ20stgw0Ffl1BRmhRhFqpCXY_2hnBLbPXn9D8LgPwdjL96xarRVQ==
< cache-control: public, max-age=31536000
< Accept-Ranges: bytes
< Age: 2903
< Date: Wed, 08 Jan 2025 08:51:12 GMT
< X-Served-By: cache-iad-kiad7000028-IAD, cache-lin1730072-LIN
< X-Cache: Miss from cloudfront, HIT, HIT
< X-Cache-Hits: 1, 0
< X-Timer: S1736326272.410959,VS0,VE1
< Strict-Transport-Security: max-age=300
< Access-Control-Allow-Methods: GET, OPTIONS
< Access-Control-Allow-Origin: *
< Server: cat factory 1.0
< X-Content-Type-Options: nosniff
<
{ [1371 bytes data]
100 35354  100 35354    0     0   212k      0 --:--:-- --:--:-- --:--:--  214k
* Connection #0 to host i.imgur.com left intact.
```

### Set a Simplified Progress Bar

You can enable a simpler progress bar with `-#` (or `--progress-bar`) option:

```powershell
curl -O -# "https://i.imgur.com/CSRiAeN.jpg"
```

This will display a progress bar using the `#` character, which will incrementally fill as the file downloads:

```
########################################################### 100.0%
```

## How to Download Multiple Files with cURL

### Range File Download

You can download multiple files with the same remote URL by specifying them using braces `{}`:

```powershell
curl -O "https://example.com/images/{1.jpg,2.jpg,3.jpg}"
```

This will download the three specified files:

```
1.jpg
2.jpg
3.jpg
```

In this particular case, you can also use the common regexp `[]` syntax:

```powershell
curl -O "https://example.com/files/file[1-3].jpg"
```

> **Note:**\
> All custom options (such as `-s` for silent mode or `--limit-rate` for bandwidth restrictions) will be applied to all the files being downloaded.

### Multiple File Download

To download files from different URLs, you need to specify the `-O` option multiple times:

```powershell
curl -O "https://i.imgur.com/CSRiAeN.jpg" -O "https://brightdata.com/wp-content/uploads/2020/12/upload_blog_20201220_153903.svg"
```

The output will contain a download bar per given URL:

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 35354  100 35354    0     0   271k      0 --:--:-- --:--:-- --:--:--  276k
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 22467    0 22467    0     0  34657      0 --:--:-- --:--:-- --:--:-- 34724
```

Similarly, you can use multiple `-o` options to define custom names for files:

```powershell
curl "https://i.imgur.com/CSRiAeN.jpg" -o "logo.jpg" "https://brightdata.com/wp-content/uploads/2020/12/upload_blog_20201220_153903.svg" -o "blog_post.svg"
```

You can also mix `-O` and `-o` options:

```powershell
curl "https://i.imgur.com/CSRiAeN.jpg" -o "logo.jpg" -O "https://brightdata.com/wp-content/uploads/2020/12/upload_blog_20201220_153903.svg"
```

> **Note:**\
> Other options like `-v`, `-s` or `--limit-rate` apply to all URLs, so they must be specified once.

## Best Practices When Downloading Files with cURL

Below is a list of some of the most important cURL file download best practices:

- **Use `curl.exe` instead of curl on Windows** to avoid the conflict with the `Invoke-WebRequest` cmdlet.
- **Ignore HTTPS and SSL/TLS errors** with the `-k` (or `--insecure`) option.
- **Specify the right HTTP methods**: Use the `-X` option to specify GET, POST, or PUT.
- **Enclose URLs in quotes and escape special characters** with `\` to avoid issues with spaces, ampersands, etc.
- **Specify a proxy to protect your identity**: Use the `-x` (or `--proxy`) option .
- **Save and reuse cookies across different requests**: Use the `-c` and `-b` options respectively.
- **Limit download speed for better control**: Use the `--limit-rate` option.
- **Add verbose output for debugging**: Use the `-v` option.
- **Check for error responses**: Always check the HTTP response codes using the `-w` option.

## cURL vs Wget for Downloading Files

- **cURL** has more granular control over data transfer, supports custom headers, authentication, and more protocols.
- **Wget** is simpler and better suited for bulk downloads, recursion, and handling interrupted transfers.

## Conclusion

Every time you make an HTTP request, you leave traces on the internet. To protect your identity, privacy, and enhance your security, consider integrating a proxy with cURL:

- [Datacenter proxies](https://brightdata.com/proxy-types/datacenter-proxies) – Over 770,000 datacenter IPs.
- [Residential proxies](https://brightdata.com/proxy-types/residential-proxies) – Over 72M residential IPs in more than 195 countries.
- [ISP proxies](https://brightdata.com/proxy-types/isp-proxies) – Over 700,000 ISP IPs.
- [Mobile proxies](https://brightdata.com/proxy-types/mobile-proxies) – Over 7M mobile IPs.

[Sign up now](https://brightdata.com) and test our proxies and scraping solutions for free!
