---
title: Discuz X3.4 任意文件删除分析
date: 2017-09-30 10:35:06
tags: 漏洞分析
---
# Discuz X3.4 任意文件删除

## 0x00 发现安全性升级
根据dz的更新[discuz diff](https://gitee.com/ComsenzDiscuz/DiscuzX/commit/7d603a197c2717ef1d7e9ba654cf72aa42d3e574)
发现删掉了几行于是开始进行分析
```php
 	if($_GET['deletefile'] && is_array($_GET['deletefile'])) {
  		foreach($_GET['deletefile'] as $key => $value) {
  			if(isset($_G['cache']['profilesetting'][$key]) && $_G['cache']['profilesetting'][$key]['formtype'] == 'file') {
 -				@unlink(getglobal('setting/attachdir').'./profile/'.$space[$key]);
 -				@unlink(getglobal('setting/attachdir').'./profile/'.$verifyinfo['field'][$key]);
  				$verifyarr[$key] = $setarr[$key] = '';
  			}
  		}
@@ -215,17 +213,14 @@ if(submitcheck('profilesubmit')) {
  				$attach['attachment'] = dhtmlspecialchars(trim($attach['attachment']));
  				if($vid && $verifyconfig['available'] && isset($verifyconfig['field'][$key])) {
  					if(isset($verifyinfo['field'][$key])) {
 -						@unlink(getglobal('setting/attachdir').'./profile/'.$verifyinfo['field'][$key]);
  						$verifyarr[$key] = $attach['attachment'];
  					}
  					continue;
  				}
  				if(isset($setarr[$key]) && $_G['cache']['profilesetting'][$key]['needverify']) {
 -					@unlink(getglobal('setting/attachdir').'./profile/'.$verifyinfo['field'][$key]);
  					$verifyarr[$key] = $attach['attachment'];
  					continue;
  				}
 -				@unlink(getglobal('setting/attachdir').'./profile/'.$space[$key]);
  				$setarr[$key] = $attach['attachment'];
  			}

```

## 0x01 漏洞分析
phpstrom + xdebug 进行分析
> upload/source/include/spacecp/spacecp_profile.php

从删掉的代码打断点进行调试
发现180行的代码中
```php
if(isset($_G['cache']['profilesetting'][$key]) && $_G['cache']['profilesetting'][$key]['formtype'] == 'file') {
                @unlink(getglobal('setting/attachdir').'./profile/'.$space[$key]);
                @unlink(getglobal('setting/attachdir').'./profile/'.$verifyinfo['field'][$key]);
```
而打印全部的(800多行不复制)
```php
$_G['cache']['profilesetting'][$key]
```
发现formtype没有等于file的
接着跟进下面的代码

```php
if($_FILES) {
		$upload = new discuz_upload();
		foreach($_FILES as $key => $file) {
			if(!isset($_G['cache']['profilesetting'][$key])) {
				continue;
			}
			$field = $_G['cache']['profilesetting'][$key];
			if((!empty($file) && $file['error'] == 0) || (!empty($space[$key]) && empty($_GET['deletefile'][$key]))) {
				$value = '1';
			} else {
				$value = '';
			}
			if(!profile_check($key, $value, $space)) {
				profile_showerror($key);
			} elseif($field['size'] && $field['size']*1024 < $file['size']) {
				profile_showerror($key, lang('spacecp', 'filesize_lessthan').$field['size'].'KB');
			}
			$upload->init($file, 'profile');
			$attach = $upload->attach;

			if(!$upload->error()) {
				$upload->save();

				if(!$upload->get_image_info($attach['target'])) {
					@unlink($attach['target']);
					continue;
				}
				$setarr[$key] = '';
				$attach['attachment'] = dhtmlspecialchars(trim($attach['attachment']));
				if($vid && $verifyconfig['available'] && isset($verifyconfig['field'][$key])) {
					if(isset($verifyinfo['field'][$key])) {
                        @unlink(getglobal('setting/attachdir').'./profile/'.$verifyinfo['field'][$key]);
						$verifyarr[$key] = $attach['attachment'];
					}
					continue;
				}
				if(isset($setarr[$key]) && $_G['cache']['profilesetting'][$key]['needverify']) {
				    @unlink(getglobal('setting/attachdir').'./profile/'.$verifyinfo['field'][$key]);
					$verifyarr[$key] = $attach['attachment'];
					continue;
				}
                @unlink(getglobal('setting/attachdir').'./profile/'.$space[$key]);
				$setarr[$key] = $attach['attachment'];
			}

		}
	}
```
这里的$space\[$key\]我们可控。也就是可以删除任意的文件。
但是查看前后的代码发现
首先要进
```php
if($_FILES)
```
所以利用时候需要一个文件进行触发
接着跟进发现
```php
$upload->get_image_info($attach['target']
```
而这里，控制了文件需要是个图片，从而保证不触发continue

## 0x02 漏洞利用
这个漏洞首先要将$space\[$key\]改成我们要的文件
而$space\[$key\]其实是用户输入的一个可控点
所以这里我吧用户的真实姓名修改为 ../../../robots.txt
然后进行保存

之后再html里面将
```html
<input name="realname" class="px" value="" tabindex="1" type="text">
```
修改为
```html
<input name="realname" class="px" value="" tabindex="1" type="file">
```
然后上传个图片就可以删掉 ../../../robots.txt

## 0x03 总结
这个漏洞之前有个很相似的。应该是没有修复彻底。
第一次跟进一个0day顺手还挖了一个src 。XD
