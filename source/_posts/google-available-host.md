title: 分享一个可以上Google的Hosts
date: 2014-08-21 13:19:55.0
tags:
- google
categories:
- 交流分享

---

谢谢WeiPhoneMac技术组的微博分享，很实在的tips！只需要将以下mapping内容加到hosts文件中就ok了。Windows hosts文件位置： C:\\Windows\\System32\\drivers\\etc\\hostsLinux hosts文件位置： /etc/hosts\#Google Services START \#Googleapis↓ 61.19.1.54 mt0....

<!-- more -->

谢谢[WeiPhoneMac技术组][WeiPhoneMac]的微博分享，很实在的tips！

只需要将以下mapping内容加到hosts文件中就ok了。

Windows hosts文件位置： C:\\Windows\\System32\\drivers\\etc\\hosts

Linux hosts文件位置： /etc/hosts

  


    #Google Services START
    #Googleapis↓
    61.19.1.54 mt0.googleapis.com
    61.19.1.54 mt1.googleapis.com
    61.19.1.54 mt2.googleapis.com
    61.19.1.54 mt3.googleapis.com
    61.19.1.54 mts0.googleapis.com
    61.19.1.54 mts1.googleapis.com
    61.19.1.54 mts2.googleapis.com
    61.19.1.54 mts3.googleapis.com
    #74.125.205.95 googleapis.com
    74.125.205.95 www.googleapis.com
    74.125.205.95 android.googleapis.com
    74.125.205.95 ajax.googleapis.com
    74.125.205.95 chart.googleapis.com
    74.125.205.95 fonts.googleapis.com
    74.125.205.95 redirector-bigcache.googleapis.com
    74.125.205.95 translate.googleapis.com
    74.125.205.95 maps.googleapis.com
    74.125.205.95 googleapis.l.google.com
    74.125.205.95 doc-0a9v4-0agdq-s-googleusercontent.commondatastorage.googleapis.com
    #Googleapis↑
    #Android↓
    74.125.205.100 android.clients.google.com
    74.125.21.100 android.l.google.com
    74.125.205.132 googlehosted.l.google.com
    61.19.1.55 base0.googlehosted.com
    61.19.1.55 base1.googlehosted.com
    61.19.1.55 base2.googlehosted.com
    61.19.1.55 base3.googlehosted.com
    61.19.1.55 base4.googlehosted.com
    61.19.1.55 base5.googlehosted.com
    173.194.70.188 mtalk.google.com
    173.194.70.125 talk.google.com
    173.194.70.125 talkx.l.google.com
    173.194.70.125 talk.l.google.com
    #Android↑
    #Youtube↓
    61.19.1.54 accounts.youtube.com
    61.19.1.54 www.youtube.com
    61.19.1.54 m.youtube.com
    61.19.1.54 insight.youtube.com
    #61.19.1.54 apiblog.youtube.com
    61.19.1.54 help.youtube.com
    61.19.1.54 i.ytimg.com
    61.19.1.54 i1.ytimg.com
    61.19.1.54 i2.ytimg.com
    61.19.1.54 i3.ytimg.com
    61.19.1.54 i4.ytimg.com
    61.19.1.54 s.ytimg.com
    61.19.1.54 ytimg.l.google.com
    #Youtube↑
    #UpLoad↓
    61.19.1.109 uploads.clients.google.com
    61.19.1.109 uploads.client.google.com
    61.19.1.109 upload.clients.google.com
    61.19.1.109 upload.client.google.com
    74.125.203.117 upload.google.com
    74.125.203.117 upload.docs.google.com
    74.125.203.117 upload.drive.google.com
    74.125.203.117 upload.video.google.com
    74.125.203.117 upload.youtube.com
    74.125.203.117 uploads.stage.gdata.youtube.com
    74.125.203.117 uploads.code.google.com
    74.125.203.117 uploads.clients.google.com
    #UpLoad↑
    #Ingress↓
    74.125.205.141 m-dot-betaspike.appspot.com
    74.125.205.141 betaspike.appspot.com
    74.125.205.141 chrometophone.appspot.com
    74.125.205.141 lfe-alpo-gm.appspot.com
    #Ingress↑
    #GoogleAccounts↓
    74.125.204.84 accounts.google.com
    74.125.204.84 accounts.l.google.com
    #74.125.204.84 accounts.youtube.com
    #GoogleAccounts↑
    #www.google.com↓
    74.125.205.147 www.google.com
    #www.google.com↑
    #AndroidGoogleMap↓
    #203.208.46.200 clients.l.google.com
    61.19.1.55 clients1.google.com
    61.19.1.55 clients2.google.com
    61.19.1.55 clients3.google.com
    61.19.1.55 clients4.google.com
    61.19.1.55 clients5.google.com
    61.19.1.55 clients6.google.com
    61.19.1.55 clients7.google.com
    #AndroidGoogleMap↑
    #GoogleDrive↓
    74.125.205.102 drive.google.com
    74.125.205.102 docs.google.com
    74.125.201.132 googledrive.com
    74.125.201.132 storage.googleapis.com
    74.125.201.132 commondatastorage.googleapis.com
    74.125.201.132 static.panoramio.com.storage.googleapis.com
    #203.208.46.134 docs0.google.com
    #203.208.46.134 docs1.google.com
    #203.208.46.134 docs2.google.com
    #203.208.46.134 docs3.google.com
    #203.208.46.134 docs4.google.com
    #203.208.46.134 docs5.google.com
    #203.208.46.134 docs6.google.com
    #203.208.46.134 docs7.google.com
    #203.208.46.134 docs8.google.com
    #203.208.46.134 docs9.google.com
    74.125.201.189 0.docs.google.com
    74.125.201.189 0.drive.google.com
    74.125.201.189 1.docs.google.com
    74.125.201.189 1.drive.google.com
    74.125.201.189 2.docs.google.com
    74.125.201.189 2.drive.google.com
    74.125.201.189 3.docs.google.com
    74.125.201.189 3.drive.google.com
    74.125.201.189 4.docs.google.com
    74.125.201.189 4.drive.google.com
    74.125.201.189 5.docs.google.com
    74.125.201.189 5.drive.google.com
    74.125.201.189 6.docs.google.com
    74.125.201.189 6.drive.google.com
    74.125.201.189 7.docs.google.com
    74.125.201.189 7.drive.google.com
    74.125.201.189 8.docs.google.com
    74.125.201.189 8.drive.google.com
    74.125.201.189 9.docs.google.com
    74.125.201.189 9.drive.google.com
    74.125.201.189 10.docs.google.com
    74.125.201.189 10.drive.google.com
    74.125.201.189 11.docs.google.com
    74.125.201.189 11.drive.google.com
    74.125.201.189 12.docs.google.com
    74.125.201.189 12.drive.google.com
    74.125.201.189 13.docs.google.com
    74.125.201.189 13.drive.google.com
    74.125.201.189 14.docs.google.com
    74.125.201.189 14.drive.google.com
    74.125.201.189 15.docs.google.com
    74.125.201.189 15.drive.google.com
    74.125.201.189 16.docs.google.com
    74.125.201.189 16.drive.google.com
    #GoogleDrive↑
    #Googleusercontent↓
    173.194.117.203 www.googleusercontent.com
    173.194.117.203 googlehosted.l.googleusercontent.com
    173.194.117.203 oauth.googleusercontent.com
    173.194.117.203 mail-attachment.googleusercontent.com
    173.194.117.203 translate.googleusercontent.com
    173.194.117.203 themes.googleusercontent.com
    173.194.117.203 webcache.googleusercontent.com
    173.194.117.203 books.googleusercontent.com
    173.194.117.203 music.googleusercontent.com
    173.194.117.203 video.googleusercontent.com
    173.194.117.203 music-onebox.googleusercontent.com
    173.194.117.203 blogger.googleusercontent.com
    173.194.117.203 code-opensocial.googleusercontent.com
    173.194.117.203 spreadsheets-opensocial.googleusercontent.com
    173.194.117.203 www-calENDar-opensocial.googleusercontent.com
    173.194.117.203 www-fc-opensocial.googleusercontent.com
    173.194.117.203 www-focus-opensocial.googleusercontent.com
    173.194.117.203 www-gm-opensocial.googleusercontent.com
    173.194.117.203 www-kix-opensocial.googleusercontent.com
    173.194.117.203 www-open-opensocial.googleusercontent.com
    173.194.117.203 www-opensocial.googleusercontent.com
    173.194.117.203 www-opensocial-sandbox.googleusercontent.com
    173.194.117.203 www-oz-opensocial.googleusercontent.com
    173.194.117.203 1-ps.googleusercontent.com
    173.194.117.203 2-ps.googleusercontent.com
    173.194.117.203 3-ps.googleusercontent.com
    173.194.117.203 4-ps.googleusercontent.com
    173.194.117.203 0-open-opensocial.googleusercontent.com
    173.194.117.203 0-focus-opensocial.googleusercontent.com
    173.194.117.203 1-focus-opensocial.googleusercontent.com
    173.194.117.203 1-open-opensocial.googleusercontent.com
    173.194.117.203 2-focus-opensocial.googleusercontent.com
    173.194.117.203 2-open-opensocial.googleusercontent.com
    173.194.117.203 3-focus-opensocial.googleusercontent.com
    173.194.117.203 3-open-opensocial.googleusercontent.com
    173.194.117.203 a-oz-opensocial.googleusercontent.com
    173.194.117.203 4fjvqid3r3oq66t548clrdj52df15coc-a-oz-opensocial.googleusercontent.com
    173.194.117.203 53rd6p0catml6vat6qra84rs0del836d-a-oz-opensocial.googleusercontent.com
    173.194.117.203 59cbv4l9s05pbaks9v77vc3mengeqors-a-oz-opensocial.googleusercontent.com
    173.194.117.203 8kubpeu8314p2efdd7jlv09an9i2ljdo-a-oz-opensocial.googleusercontent.com
    173.194.117.203 adstvca8k2ooaknjjmv89j22n9t676ve-a-oz-opensocial.googleusercontent.com
    173.194.117.203 bt26mravu2qpe56n8gnmjnpv2inl84bf-a-oz-opensocial.googleusercontent.com
    173.194.117.203 debh8vg7vd93bco3prdajidmm7dhql3f-a-oz-opensocial.googleusercontent.com
    173.194.117.203 hsco54a20sh11q9jkmb51ad2n3hmkmrg-a-oz-opensocial.googleusercontent.com
    173.194.117.203 k6v18tjr24doa89b55o3na41kn4v73eb-a-oz-opensocial.googleusercontent.com
    173.194.117.203 i8brh95qor6r54nkl52hidj2ggcs4jgm-a-oz-opensocial.googleusercontent.com
    173.194.117.203 ob7f2qc0i50kbjnc81vkhgmb5hsv7a8l-a-oz-opensocial.googleusercontent.com
    173.194.117.203 qhie5b8u979rnch1q0hqbrmbkn9estf7-a-oz-opensocial.googleusercontent.com
    173.194.117.203 r70rmsn4s0rhk6cehcbbcbfbs31pu0va-a-oz-opensocial.googleusercontent.com
    173.194.117.203 rbjhe237k979f79d87gmenp3gejfonu9-a-oz-opensocial.googleusercontent.com
    173.194.117.203 u807isd5egseeabjccgcns005p2miucq-a-oz-opensocial.googleusercontent.com
    173.194.117.203 upt14k1i2veesusrda9nfotcrbp9d7p5-a-oz-opensocial.googleusercontent.com
    173.194.117.203 4lam9a1ki27mb9p1h5q3furvvf58ss02-a-gm-opensocial.googleusercontent.com
    173.194.117.203 ode25pfjgmvpquh3b1oqo31ti5ibg5fr-a-calENDar.opensocial.googleusercontent.com
    173.194.117.203 3hdrrlnlknhi77nrmsjnjr152ueo3soc-a-calENDar-opensocial.googleusercontent.com
    173.194.117.202 clients1.googleusercontent.com
    173.194.117.202 clients2.googleusercontent.com
    173.194.117.202 clients3.googleusercontent.com
    173.194.117.202 clients4.googleusercontent.com
    173.194.117.202 clients5.googleusercontent.com
    173.194.117.202 clients6.googleusercontent.com
    173.194.117.202 clients7.googleusercontent.com
    173.194.117.203 feedback.googleusercontent.com
    173.194.117.203 images1-focus-opensocial.googleusercontent.com
    173.194.117.203 images2-focus-opensocial.googleusercontent.com
    173.194.117.203 images3-focus-opensocial.googleusercontent.com
    173.194.117.203 images4-focus-opensocial.googleusercontent.com
    173.194.117.203 images5-focus-opensocial.googleusercontent.com
    173.194.117.203 images6-focus-opensocial.googleusercontent.com
    173.194.117.203 images7-focus-opensocial.googleusercontent.com
    173.194.117.203 images8-focus-opensocial.googleusercontent.com
    173.194.117.203 images9-focus-opensocial.googleusercontent.com
    173.194.117.203 images1-esmobile-opensocial.googleusercontent.com
    173.194.117.203 images2-esmobile-opensocial.googleusercontent.com
    173.194.117.203 images3-esmobile-opensocial.googleusercontent.com
    173.194.117.203 images4-esmobile-opensocial.googleusercontent.com
    173.194.117.203 images5-esmobile-opensocial.googleusercontent.com
    173.194.117.203 images6-esmobile-opensocial.googleusercontent.com
    173.194.117.203 images7-esmobile-opensocial.googleusercontent.com
    173.194.117.203 images8-esmobile-opensocial.googleusercontent.com
    173.194.117.203 images9-esmobile-opensocial.googleusercontent.com
    173.194.117.203 images-docs-opensocial.googleusercontent.com
    173.194.117.203 images-oz-opensocial.googleusercontent.com
    173.194.117.203 lh0.googleusercontent.com
    173.194.117.203 lh1.googleusercontent.com
    173.194.117.203 lh2.googleusercontent.com
    173.194.117.203 lh3.googleusercontent.com
    173.194.117.203 lh4.googleusercontent.com
    173.194.117.203 lh5.googleusercontent.com
    173.194.117.203 lh6.googleusercontent.com
    173.194.117.202 lh0.ggpht.com
    173.194.117.202 lh1.ggpht.com
    173.194.117.202 lh2.ggpht.com
    173.194.117.202 lh3.ggpht.com
    173.194.117.202 lh4.ggpht.com
    173.194.117.202 lh5.ggpht.com
    173.194.117.202 lh6.ggpht.com
    173.194.117.202 nt0.ggpht.com
    173.194.117.202 nt1.ggpht.com
    173.194.117.202 nt2.ggpht.com
    173.194.117.202 nt3.ggpht.com
    173.194.117.202 nt4.ggpht.com
    173.194.117.202 nt5.ggpht.com
    173.194.117.202 nt6.ggpht.com
    173.194.117.202 yt0.ggpht.com
    173.194.117.202 yt1.ggpht.com
    173.194.117.202 yt2.ggpht.com
    173.194.117.202 yt3.ggpht.com
    173.194.117.202 yt4.ggpht.com
    173.194.117.202 yt5.ggpht.com
    173.194.117.202 yt6.ggpht.com
    173.194.117.203 ci1.googleusercontent.com
    173.194.117.203 ci2.googleusercontent.com
    173.194.117.203 ci3.googleusercontent.com
    173.194.117.203 ci4.googleusercontent.com
    173.194.117.203 ci5.googleusercontent.com
    173.194.117.203 ci6.googleusercontent.com
    173.194.117.203 s1.googleusercontent.com
    173.194.117.203 s2.googleusercontent.com
    173.194.117.203 s3.googleusercontent.com
    173.194.117.203 s4.googleusercontent.com
    173.194.117.203 s5.googleusercontent.com
    173.194.117.203 s6.googleusercontent.com
    #Googleusercontent
    74.125.205.113 apis.google.com
    74.125.205.83 mail.google.com
    74.125.205.102 play.google.com
    61.19.1.54 keep.google.com
    61.19.1.54 plus.google.com
    61.19.1.54 plus.url.google.com
    61.19.1.54 plusone.google.com
    74.125.205.138 talkgadget.google.com
    61.19.1.54 safebrowsing.cache.l.google.com
    61.19.1.54 safebrowsing.clients.google.com
    61.19.1.54 safebrowsing.google.com
    61.19.1.54 safebrowsing-cache.google.com
    61.19.1.54 developer.android.com
    61.19.1.54 market.android.com
    #74.125.31.121 www.chromium.org
    #74.125.31.121 chromium.org
    #74.125.31.121 codereview.chromium.org
    #74.125.31.121 dev.chromium.org
    74.125.136.94 csi.l.google.com
    74.125.205.120 csi.gstatic.com
    61.19.1.54 ssl.gstatic.com
    74.125.205.120 encrypted-tbn0.gstatic.com
    74.125.205.120 encrypted-tbn1.gstatic.com
    74.125.205.120 encrypted-tbn2.gstatic.com
    74.125.205.120 encrypted-tbn3.gstatic.com
    74.125.205.120 encrypted-tbn4.gstatic.com
    74.125.205.120 encrypted-tbn5.gstatic.com
    74.125.205.120 encrypted-tbn6.gstatic.com
    61.19.1.54 g0.gstatic.com
    61.19.1.54 g1.gstatic.com
    61.19.1.54 g2.gstatic.com
    61.19.1.54 g3.gstatic.com
    61.19.1.54 mt0.gstatic.com
    61.19.1.54 mt1.gstatic.com
    61.19.1.54 mt2.gstatic.com
    61.19.1.54 mt3.gstatic.com
    61.19.1.54 mt4.gstatic.com
    61.19.1.54 mt5.gstatic.com
    61.19.1.54 mt6.gstatic.com
    61.19.1.54 mt7.gstatic.com
    61.19.1.54 t0.gstatic.com
    61.19.1.54 t1.gstatic.com
    61.19.1.54 t2.gstatic.com
    61.19.1.54 t3.gstatic.com
    61.19.1.54 www.gstatic.com
    61.19.1.54 maps.gstatic.com
    #61.19.1.54 mobile.l.google.com
    #61.19.1.54 mobile-gtalk.l.google.com
    61.19.1.54 wallet.google.com
    61.19.1.54 answers.google.com
    61.19.1.54 chart.apis.google.com
    61.19.1.54 appengine.google.com
    61.19.1.54 apps.google.com
    61.19.1.54 appspot.l.google.com
    61.19.1.54 bks0.books.google.com
    61.19.1.54 bks1.books.google.com
    61.19.1.54 bks10.books.google.com
    61.19.1.54 bks2.books.google.com
    61.19.1.54 bks3.books.google.com
    61.19.1.54 bks4.books.google.com
    61.19.1.54 bks5.books.google.com
    61.19.1.54 bks6.books.google.com
    61.19.1.54 bks7.books.google.com
    61.19.1.54 bks8.books.google.com
    61.19.1.54 bks9.books.google.com
    61.19.1.54 blogsearch.google.com
    61.19.1.54 books.google.com
    61.19.1.54 browserchannel-docs.l.google.com
    61.19.1.54 browserchannel-spreadsheets.l.google.com
    61.19.1.54 browsersync.google.com
    61.19.1.54 browsersync.l.google.com
    61.19.1.54 buzz.google.com
    61.19.1.54 cache.l.google.com
    61.19.1.54 cache.pack.google.com
    61.19.1.54 calENDar.google.com
    61.19.1.54 cbk0.google.com
    61.19.1.54 cbk1.google.com
    61.19.1.54 cbk2.google.com
    61.19.1.54 cbk3.google.com
    61.19.1.54 cbks0.google.com
    61.19.1.54 cbks1.google.com
    61.19.1.54 cbks2.google.com
    61.19.1.54 cbks3.google.com
    61.19.1.54 chatenabled.mail.google.com
    61.19.1.54 checkout.google.com
    61.19.1.54 checkout.l.google.com
    61.19.1.54 chrome.google.com
    61.19.1.54 code.google.com
    61.19.1.54 code.l.google.com
    61.19.1.54 desktop.google.com
    61.19.1.54 desktop.l.google.com
    61.19.1.54 desktop2.google.com
    61.19.1.54 developers.google.com
    61.19.1.54 ditu.google.com
    61.19.1.54 dl.google.com
    61.19.1.54 dl.l.google.com
    61.19.1.54 dl-ssl.google.com
    61.19.1.54 earth.google.com
    61.19.1.54 encrypted.google.com
    61.19.1.54 encrypted-tbn.l.google.com
    61.19.1.54 encrypted-tbn0.google.com
    61.19.1.54 encrypted-tbn1.google.com
    61.19.1.54 encrypted-tbn2.google.com
    61.19.1.54 encrypted-tbn3.google.com
    61.19.1.54 feedburner.google.com
    61.19.1.54 feedproxy.google.com
    61.19.1.54 filetransferenabled.mail.google.com
    61.19.1.54 finance.google.com
    61.19.1.54 fusion.google.com
    61.19.1.54 geoauth.google.com
    61.19.1.54 gg.google.com
    61.19.1.54 ghs.google.com
    61.19.1.54 ghs.l.google.com
    61.19.1.54 ghs46.google.com
    61.19.1.54 ghs46.l.google.com
    61.19.1.54 google.com
    61.19.1.54 googlecode.l.google.com
    61.19.1.54 google-public-dns-a.google.com
    61.19.1.54 google-public-dns-b.google.com
    61.19.1.54 goto.google.com
    61.19.1.54 groups.google.com
    61.19.1.54 groups.l.google.com
    61.19.1.54 groups-beta.google.com
    61.19.1.54 gxc.google.com
    61.19.1.54 id.google.com
    61.19.1.54 id.l.google.com
    61.19.1.54 images.google.com
    61.19.1.54 images.l.google.com
    61.19.1.54 investor.google.com
    61.19.1.54 jmt0.google.com
    61.19.1.54 kh.google.com
    61.19.1.54 kh.l.google.com
    61.19.1.54 khm.google.com
    61.19.1.54 khm.l.google.com
    61.19.1.54 khm0.google.com
    61.19.1.54 khm1.google.com
    61.19.1.54 khm2.google.com
    61.19.1.54 khm3.google.com
    61.19.1.54 khmdb.google.com
    61.19.1.54 khms.google.com
    61.19.1.54 khms.l.google.com
    61.19.1.54 khms0.google.com
    61.19.1.54 khms1.google.com
    61.19.1.54 khms2.google.com
    61.19.1.54 khms3.google.com
    61.19.1.54 labs.google.com
    61.19.1.54 large-uploads.l.google.com
    61.19.1.54 lh2.google.com
    61.19.1.54 lh2.l.google.com
    61.19.1.54 lh3.google.com
    61.19.1.54 lh4.google.com
    61.19.1.54 lh5.google.com
    61.19.1.54 lh6.google.com
    61.19.1.54 linkhelp.clients.google.com
    61.19.1.54 local.google.com
    61.19.1.54 m.google.com
    61.19.1.54 map.google.com
    61.19.1.54 maps.google.com
    61.19.1.54 maps.l.google.com
    61.19.1.54 maps-api-ssl.google.com
    61.19.1.54 mapsengine.google.com
    61.19.1.54 mars.google.com
    61.19.1.54 mobilemaps.clients.google.com
    61.19.1.54 mt.google.com
    61.19.1.54 mt.l.google.com
    61.19.1.54 mt0.google.com
    61.19.1.54 mt1.google.com
    61.19.1.54 mt2.google.com
    61.19.1.54 mt3.google.com
    61.19.1.54 mts.google.com
    61.19.1.54 mts.l.google.com
    61.19.1.54 mts0.google.com
    61.19.1.54 mts1.google.com
    61.19.1.54 mts2.google.com
    61.19.1.54 mts3.google.com
    61.19.1.54 music.google.com
    61.19.1.54 music-streaming.l.google.com
    61.19.1.54 mw1.google.com
    61.19.1.54 mw2.google.com
    61.19.1.54 news.google.com
    61.19.1.54 news.l.google.com
    61.19.1.54 pack.google.com
    61.19.1.54 photos.google.com
    61.19.1.54 photos-ugc.l.google.com
    61.19.1.54 picasa.google.com
    61.19.1.54 picasaweb.google.com
    61.19.1.54 picasaweb.l.google.com
    61.19.1.54 places.google.com
    61.19.1.54 productforums.google.com
    61.19.1.54 profiles.google.com
    61.19.1.54 reader.google.com
    61.19.1.54 sandbox.google.com
    61.19.1.54 sb.google.com
    61.19.1.54 sb.l.google.com
    61.19.1.54 sb-ssl.google.com
    61.19.1.54 sb-ssl.l.google.com
    61.19.1.54 scholar.google.com
    61.19.1.54 scholar.l.google.com
    61.19.1.54 script.google.com
    61.19.1.54 services.google.com
    61.19.1.54 sites.google.com
    61.19.1.54 sketchup.google.com
    61.19.1.54 sketchup.l.google.com
    61.19.1.54 spreadsheet.google.com
    61.19.1.54 spreadsheets.google.com
    61.19.1.54 spreadsheets.l.google.com
    61.19.1.54 spreadsheets0.google.com
    61.19.1.54 spreadsheets1.google.com
    61.19.1.54 spreadsheets2.google.com
    61.19.1.54 spreadsheets3.google.com
    61.19.1.54 spreadsheets4.google.com
    61.19.1.54 spreadsheets5.google.com
    61.19.1.54 spreadsheets6.google.com
    61.19.1.54 spreadsheets7.google.com
    61.19.1.54 spreadsheets8.google.com
    61.19.1.54 spreadsheets9.google.com
    61.19.1.54 spreadsheets-china.l.google.com
    61.19.1.54 suggestqueries.google.com
    61.19.1.54 suggestqueries.l.google.com
    61.19.1.54 support.google.com
    61.19.1.54 tbn0.google.com
    61.19.1.54 tbn1.google.com
    61.19.1.54 tbn2.google.com
    61.19.1.54 tbn3.google.com
    61.19.1.54 toolbar.google.com
    61.19.1.54 toolbarqueries.clients.google.com
    61.19.1.54 toolbarqueries.google.com
    61.19.1.54 toolbarqueries.l.google.com
    61.19.1.54 tools.google.com
    61.19.1.54 tools.l.google.com
    61.19.1.54 translate.google.com
    61.19.1.54 trENDs.google.com
    61.19.1.54 video.google.com
    61.19.1.54 video-stats.l.google.com
    61.19.1.54 voice.google.com
    61.19.1.54 wifi.google.com
    61.19.1.54 wifi.l.google.com
    61.19.1.54 wire.l.google.com
    61.19.1.54 writely.google.com
    61.19.1.54 writely.l.google.com
    61.19.1.54 writely-china.l.google.com
    61.19.1.54 writely-com.l.google.com
    61.19.1.54 www.l.google.com
    61.19.1.54 www2.l.google.com
    61.19.1.54 www3.l.google.com
    61.19.1.54 www4.l.google.com
    61.19.1.54 ytstatic.l.google.com
    61.19.1.54 appspot.com
    61.19.1.54 chrometophone.appspot.com
    61.19.1.54 evolutionofweb.appspot.com
    61.19.1.54 googcloudlabs.appspot.com
    61.19.1.54 gv-gadget.appspot.com
    61.19.1.54 magnifier.blogspot.com
    61.19.1.54 moderator.appspot.com
    61.19.1.54 newsfeed-dot-latest-dot-rovio-ad-engine.appspot.com
    61.19.1.54 productideas.appspot.com
    61.19.1.54 project-slingshot-gp.appspot.com
    61.19.1.54 r2303.latest.project-slingshot-hr.appspot.com
    61.19.1.54 r3085-dot-latest-dot-project-slingshot-gp.appspot.com
    61.19.1.54 r3091-dot-latest-dot-project-slingshot-gp.appspot.com
    61.19.1.54 r3101-dot-latest-dot-project-slingshot-gp.appspot.com
    61.19.1.54 r3269-dot-latest-dot-project-slingshot-gp.appspot.com
    61.19.1.54 r3432-dot-latest-dot-project-slingshot-hr.appspot.com
    61.19.1.54 r4681-dot-latest-dot-project-slingshot-hr.appspot.com
    61.19.1.54 r7647-dot-latest-dot-project-slingshot-hr.appspot.com
    61.19.1.54 wcproxyx.appspot.com
    61.19.1.54 www.appspot.com
    61.19.1.54 autoproxy-gfwlist.googlecode.com
    61.19.1.54 chromium.googlecode.com
    61.19.1.54 closure-library.googlecode.com
    61.19.1.54 earth-api-samples.googlecode.com
    61.19.1.54 gmaps-samples-flash.googlecode.com
    61.19.1.54 google-code-feed-gadget.googlecode.com
    61.19.1.54 imoutohost.googlecode.com
    61.19.1.54 smarthosts.googlecode.com
    61.19.1.54 huhamhire-hosts.googlecode.com
    61.19.1.54 blogsearch.google.cn
    61.19.1.54 g.cn
    61.19.1.54 www.g.cn
    61.19.1.54 ditu.google.cn
    61.19.1.54 maps.google.cn
    61.19.1.54 gg.google.cn
    61.19.1.54 id.google.cn
    61.19.1.54 maps.gstatic.cn
    61.19.1.54 m.google.cn
    61.19.1.54 mt.google.cn
    61.19.1.54 mt0.google.cn
    61.19.1.54 mt1.google.cn
    61.19.1.54 mt2.google.cn
    61.19.1.54 mt3.google.cn
    61.19.1.54 news.google.cn
    61.19.1.54 scholar.google.cn
    61.19.1.54 translate.google.cn
    61.19.1.54 www.google.cn
    61.19.1.54 www.gstatic.cn
    61.19.1.54 accounts.google.com.hk
    61.19.1.54 blogsearch.google.com.hk
    61.19.1.54 books.google.com.hk
    61.19.1.54 clients1.google.com.hk
    61.19.1.54 desktop.google.com.hk
    61.19.1.54 encrypted.google.com.hk
    61.19.1.54 groups.google.com.hk
    61.19.1.54 gxc.google.com.hk
    61.19.1.54 id.google.com.hk
    61.19.1.54 images.google.com.hk
    #61.19.1.54 m.google.com.hk
    61.19.1.54 maps.google.com.hk
    61.19.1.54 news.google.com.hk
    61.19.1.54 picasaweb.google.com.hk
    61.19.1.54 plus.url.google.com.hk
    61.19.1.54 scholar.google.com.hk
    61.19.1.54 toolbar.google.com.hk
    61.19.1.54 toolbarqueries.google.com.hk
    61.19.1.54 translate.google.com.hk
    61.19.1.54 wENDa.google.com.hk
    61.19.1.54 www.google.com.hk
    61.19.1.54 accounts.google.com.tw
    61.19.1.54 blogsearch.google.com.tw
    61.19.1.54 books.google.com.tw
    61.19.1.54 clients1.google.com.tw
    61.19.1.54 desktop.google.com.tw
    61.19.1.54 encrypted.google.com.tw
    61.19.1.54 groups.google.com.tw
    61.19.1.54 gxc.google.com.tw
    61.19.1.54 id.google.com.tw
    61.19.1.54 images.google.com.tw
    #61.19.1.54 m.google.com.tw
    61.19.1.54 maps.google.com.tw
    61.19.1.54 news.google.com.tw
    61.19.1.54 picasaweb.google.com.tw
    61.19.1.54 plus.url.google.com.tw
    61.19.1.54 scholar.google.com.tw
    61.19.1.54 toolbar.google.com.tw
    61.19.1.54 toolbarqueries.google.com.tw
    61.19.1.54 translate.google.com.tw
    61.19.1.54 www.google.com.tw
    61.19.1.54 accounts.google.com.sg
    61.19.1.54 blogsearch.google.com.sg
    61.19.1.54 books.google.com.sg
    61.19.1.54 clients1.google.com.sg
    61.19.1.54 desktop.google.com.sg
    61.19.1.54 encrypted.google.com.sg
    61.19.1.54 groups.google.com.sg
    61.19.1.54 gxc.google.com.sg
    61.19.1.54 id.google.com.sg
    61.19.1.54 images.google.com.sg
    #61.19.1.54 m.google.com.sg
    61.19.1.54 maps.google.com.sg
    61.19.1.54 news.google.com.sg
    61.19.1.54 picasaweb.google.com.sg
    61.19.1.54 plus.url.google.com.sg
    61.19.1.54 scholar.google.com.sg
    61.19.1.54 toolbar.google.com.sg
    61.19.1.54 toolbarqueries.google.com.sg
    61.19.1.54 translate.google.com.sg
    61.19.1.54 www.google.com.sg
    61.19.1.54 accounts.google.co.jp
    61.19.1.54 blogsearch.google.co.jp
    61.19.1.54 books.google.co.jp
    61.19.1.54 clients1.google.co.jp
    61.19.1.54 desktop.google.co.jp
    61.19.1.54 encrypted.google.co.jp
    61.19.1.54 groups.google.co.jp
    61.19.1.54 gxc.google.com.co.jp
    61.19.1.54 id.google.com.co.jp
    61.19.1.54 images.google.co.jp
    #61.19.1.54 m.google.com.co.jp
    61.19.1.54 maps.google.co.jp
    61.19.1.54 news.google.co.jp
    61.19.1.54 picasaweb.google.co.jp
    61.19.1.54 plus.url.google.co.jp
    61.19.1.54 scholar.google.co.jp
    61.19.1.54 toolbar.google.co.jp
    61.19.1.54 toolbarqueries.google.co.jp
    61.19.1.54 translate.google.co.jp
    61.19.1.54 www.google.co.jp
    61.19.1.54 android.googlesource.com
    61.19.1.54 auth.keyhole.com
    61.19.1.54 chrome.angrybirds.com
    61.19.1.54 developer.chrome.com
    61.19.1.54 domains.googlesyndication.com
    61.19.1.54 earthengine.googlelabs.com
    61.19.1.54 g.co
    61.19.1.54 gmail.com
    61.19.1.54 goo.gl
    61.19.1.54 listen.googlelabs.com
    61.19.1.54 m.gmail.com
    61.19.1.54 m.googlemail.com
    61.19.1.54 ngrams.googlelabs.com
    61.19.1.54 ssl.google-analytics.com
    61.19.1.54 www.gmail.com
    61.19.1.54 www.googleadservices.com
    61.19.1.54 www.google-analytics.com
    61.19.1.54 www.googlelabs.com
    61.19.1.54 www.googlesource.com
    61.19.1.54 chatenabled.mail.google.com
    61.19.1.54 panoramio.com
    61.19.1.54 www.panoramio.com
    61.19.1.54 static.panoramio.com
    61.19.1.54 googleblog.blogspot.com
    61.19.1.54 googlechinablog.blogspot.com
    #Google Services END

  



[WeiPhoneMac]: http://www.weibo.com/3146485692/Bj9dE1SC5