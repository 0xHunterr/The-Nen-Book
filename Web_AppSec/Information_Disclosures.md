- [ ] Check SW version numbers and look for public exploits
- [ ] Try path traversal
- [ ] Search the waybackmachine https://web.archive.org/web/*/example.com/*  (list all urls)
	- [ ] Search for (/admin, .conf, .env, .js, .php) in the listed urls 
- [ ] Search for pastebin dumps using their API or tools like PasteHunter or [Pastebin-scraper](https://github.com/streaak/pastebin-scraper/) can also automate the process. (`./scrape.sh -g KEYWORD`)
- [ ] Check for .git Directory (ex->`https://example.com/.git`) if it listed contents u can download the Dir using `wget -r example.com/.git`
	- [ ] if it didn't listed and u got something like 403, check `curl https://example.com/.git/config` and try access the `.git/HEAD`, traverse through the directory and download the files u want 
	- [ ] the work flow will be something like this ```
```
$ cat .git/HEAD
ref: refs/heads/master

$ cat .git/refs/heads/master
0a66452433322af3d319a377415a890c70bbd263

/*this command make u know what the type of objects*/
$ git cat-file -t 0a66452433322af3d319a377415a890c70bbd263 
commit

$ git cat-file -p 0a66452433322af3d319a377415a890c70bbd263
tree 0a72e6850ef963c6aeee4121d38cf9de773865d8

$ git cat-file -p 0a72e6850ef963c6aeee4121d38cf9de773865d8 100644 blob 6ad5fb6b9a351a77c396b5f1163cc3b0abcde895 .gitignore 040000 blob 4b66088945aab8b967da07ddd8d3cf8c47a3f53c source.py 040000 blob 9a3227dca45b3977423bb1296bbc312316c2aa0d README 040000 tree 3b1127d12ee43977423bb1296b8900a316c2ee32 resources

```
- [ ] decompressing git files using python 
    `python -c 'import zlib, sys; print repr(zlib.decompress(sys.stdin.read()))' < OBJECT_FILE`
- [ ] Check the Page Source take note of where the application displays or uses your personal information. 
	- [ ] Then right-click those pages and click View page source. You should see the HTML source code of the current page. Follow the links on this page to find other HTML files and JavaScript files ,grep every page for hardcoded credentials, API keys, and personal information with keywords like password and api_key. locate JavaScript files on a site by using tools like [LinkFinder](https://github.com/GerbenJavado/LinkFinder/)
- [ ] use jsleak tool