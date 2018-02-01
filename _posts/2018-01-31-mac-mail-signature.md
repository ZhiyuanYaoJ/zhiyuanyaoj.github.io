---
title: Mac 上自定义 Mail 的签名
date: 2018-01-31 11:30:00
description: 给自己的邮件一个好看的签名！
categories:
- HTML
tags:
- HTML
photos:
---

## 简介

越长大越发现有一个优雅的邮件签名是一件非常重要的事情，不光可以更加方便大家找到关于自己的信息，还能凸显自己非常绅士的生活态度。所以今天就来记录一下自己捣鼓的新签名。先来显摆一下我的新签名：

![新签名](https://ericyaoj.github.io/assets/images/X/mac_mail_sign.png)

下面就来简单记录一下我做签名的流程。

## 过程

### 建立签名，并关闭 Mail

首先我们要打开 Mac 上面的 Mail 应用，并且建立新的签名。

![建立签名](http://cloudfront.coneybeare.me/blog/assets/2017-09-30-how-to-make-an-html-signature-in-apple-mail-for-high-sierra-os-x-10-dot-13/2-30235d0ba15f785bfb7340d0109d4b57.jpg)

这样，一个签名对应的占位文件就会在系统中自动生成。我们稍后就要对这个文件进行处理，但处理之前一定要记得关闭 Mail 应用。

> 注意：
> - 只要建立了一个签名，就可以通过拖动的方式把这个签名加到其他的用户中。
> - 记得要关闭 Mail，否则 Mail 会自动覆盖我们稍后做的更改。

### 找到签名文件

Mac 真的是会把这样的文件隐藏的很好，让我们很难找到。但是还是有机智的大神帮忙找到了。

```shell
$ cd ~/Library/Mail/V4/MailData/Signatures/
$ ls -laht ./
total 56
drwxr-xr-x  21 andre  staff   672B Jan 31 22:26 ..
-rw-r--r--@  1 andre  staff   882B Jan 31 22:26 AccountsMap.plist
-rw-r--r--@  1 andre  staff    17K Jan 31 22:25 304BA93C-****-****-****-***********.mailsignature
-rw-r--r--@  1 andre  staff   383B Jan 31 21:53 AllSignatures.plist
drwxr-xr-x@  5 andre  staff   160B Jan 31 21:53 .
```

这样我们就能看到类似 `*.mailsignature` 的文件了。这个文件就是我们刚刚建立的签名格式文件了。

> 注意：
> - 如果使用 **iCloud** 的话，文件的位置可能是 `~/Library/Mobile\ Documents/com~apple~mail/Data/V4/Signatures/`，不过具体的文件夹位置大家可以根据自己情况大概定位一下。比如，我的经验是在我的 Mac 上一直到 `~Library/Mail/` 都没问题，但是我的签名在此后的 `V2` 文件夹里。

### 对文件进行编辑

找到文件之后，只要通过 **TextEdit** 打开就可以进行编辑了。编辑的过程中要注意，只编辑 `html` 部分的代码，上面的 metadata 不要动。

![编辑文件](http://cloudfront.coneybeare.me/blog/assets/2017-09-30-how-to-make-an-html-signature-in-apple-mail-for-high-sierra-os-x-10-dot-13/7-5fd8e01b4d97e8ccadf22801bb6965a5.jpg)

在这里给出我自己签名的 `html` 代码：

```html
<body>
<br />
<br />
<br />
<style type='text/css'>
  a{margin:0;padding:0;border:none !important;text-decoration:none !important;}
  table,tr,td,a,span{font-family:'Helvetica Neue',Helvetica,Arial,sans-serif;}
</style>
<meta http-equiv='Content-Type' content='text/html; charset=UTF-8' />
<meta name='format-detection' content='telephone=no' />
<meta name='format-detection' content='address=no' />
<meta name='format-detection' content='email=no' />

<table id="sig" width='320' cellspacing='0' cellpadding='0' border-spacing='0' style="width:320px;margin:0;padding:0;">
    <tr>
        <td valign='top' width="85" style="width:100px;margin:0;padding:0;vertical-align:top;">
            <a href='https://www.zhiyuanyao.com' title="Zhiyuan YAO" style="border:none;text-decoration:none;">
        <img moz-do-not-send="true" src="https://www.zhiyuanyao.com/assets/images/x_logo_vert.png" alt="Zhiyuan YAO" width='78' height='100' style="border:none;width:78px;height:100px;display:block;">
      </a>
        </td>

    <td width="10" style="width:10px;min-width:10px;max-width:10px;margin:0;padding:0;">&nbsp;</td>

        <td style="margin:0;padding:0;">
            <table id="sig2" cellspacing='0' cellpadding='0' border-spacing='0' style="padding:0;margin:0;font-family:'Helvetica Neue',Helvetica,Arial,sans-serif;font-size:12px;mso-line-height-rule:exactly;line-height:14px;color:#b0b0b0;border-collapse:collapse;-webkit-text-size-adjust:none;">
                <tr style="margin:0;padding:0;">
                    <td style="margin:0;padding:0;font-family:'Helvetica Neue',Helvetica,Arial,sans-serif;white-space:nowrap;">
                        <strong><a href="mailto:zhiyuan.yao@polytechnique.edu " style="border:none;text-decoration:none;color:#049cdb;"><span style="color:#049cdb">Zhiyuan YAO</span></a></strong>
                    </td>
                </tr>

        <tr height='4' style="height:1px;max-height:1px;font-size:1px;mso-line-height-rule:exactly;line-height:1px;">
            <td height='4' style="height:1px;max-height:1px;font-size:1px;mso-line-height-rule:exactly;line-height:1px;">&nbsp;</td>
        </tr>
        
                <tr style="margin:0;padding:0;">
                    <td style="margin:0;padding:0;font-family:'Helvetica Neue',Helvetica,Arial,sans-serif;white-space:nowrap;">
                        <a href="http://co4dis.fr/" style="border:none;text-decoration:none;color:#b0b0b0;"><span style="color:#b0b0b0">Graduate Degree | IoT - Innovation and Management</span></a>
                    </td>
                </tr>

        <tr height='3' style="height:1px;max-height:1px;font-size:1px;mso-line-height-rule:exactly;line-height:1px;"></tr>
        
                <tr style="margin:0;padding:0;">
                    <td style="margin:0;padding:0;font-family:'Helvetica Neue',Helvetica,Arial,sans-serif;white-space:nowrap;">
                        <a href="http://www.polytechnique.edu/" style="border:none;text-decoration:none;color:#b0b0b0;"><span style="color:#b0b0b0">Ecole Polytechnique</span></a>
                    </td>
                </tr>

        <tr height='8' style="height:4px;max-height:4px;font-size:4px;mso-line-height-rule:exactly;line-height:4px;">
            <td height='8' style="height:4px;max-height:4px;font-size:4px;mso-line-height-rule:exactly;line-height:4px;">&nbsp;</td>
        </tr>
        
                <tr style="margin:0;padding:0;">
                    <td style="margin:0;padding:0;font-family:'Helvetica Neue',Helvetica,Arial,sans-serif;white-space:nowrap;">
                        <span style="color:#b0b0b0">58 Rue de Sèvres</span> <span style="color:#e0e0e0">&bull;</span> <span style="color:#b0b0b0">75007 Paris</span> <span style="color:#e0e0e0">&bull;</span> <span style="color:#b0b0b0">FRANCE</span>
                    </td>
                </tr>

        <tr height='3' style="height:1px;max-height:1px;font-size:1px;mso-line-height-rule:exactly;line-height:1px;"></tr>
        
                <tr style="margin:0;padding:0;">
                    <td style="margin:0;padding:0;font-family:'Helvetica Neue',Helvetica,Arial,sans-serif;white-space:nowrap;">
                        <a href="tel:+33-6-60-30-28-14" style="border:none;text-decoration:none;color:#b0b0b0;"><span style="color:#b0b0b0">+33 6 60 30 28 14</span></a> <span style="color:#e0e0e0">&bull;</span> <a href="https://www.zhiyuanyao.com" style="border:none;text-decoration:none;color:#b0b0b0;"><span style="color:#b0b0b0">zhiyuanyao.com</span></a>
                    </td>
                </tr>

            </table>
        </td>
    </tr>
</table>
<br />
&nbsp;
</body>
```

把文件下面的 `html` 文件替换掉之后还没有结束，为了防止 Mac 自动修改、用旧的签名覆盖我们自定义的签名，我们需要把文件锁住，具体方法则为：

```shell
chflags uchg ~/Library/Mail/V4/MailData/Signatures/*.mailsignature
```

> 注意：
> - 如果希望不断的更改的话，还会用到解锁的命令行：
>       chflags nouchg ~/Library/Mail/V4/MailData/Signatures/*.mailsignature

### 重新打开 Mail

上述步骤都完成之后，就可以把 Mail 打开，写一封邮件检验一下我们新的签名啦！

## Ref
- [How to Make an HTML Signature in Apple Mail for High Sierra OS X 10.13](http://matt.coneybeare.me/how-to-make-an-html-signature-in-apple-mail-for-high-sierra-os-x-10-dot-13/)
- [How to Make an HTML Signature in Apple Mail for Sierra OS X 10.12](http://matt.coneybeare.me/how-to-make-an-html-signature-in-apple-mail-for-sierra-os-x-10-dot-12/)