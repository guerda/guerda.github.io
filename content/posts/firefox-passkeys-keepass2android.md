+++
title = 'Use passkeys on Firefox and Keepass2Android'
date = 2025-02-16T16:15:10+01:00
tags = ['Passkeys', 'Android', 'KeePass2Android', 'KeePass', 'Mozilla Firefox', 'Firefox']
+++

Because I found out via try & error: some pages don't find their passkeys in Firefox on Android, even though when Firefox just created it on this page. This happens if you have a password service  like [Keepass2Android](https://philipp.crocoll.net/keepass2android/index.php) setup as default. Creation of passkeys works fine and they appear in the Google Wallet. In Chrome the whole flow just works.

Easy solution: setup Google Wallet as your default password service, then switch to Keepass2Android again and add Wallet as additional service.

Do this via Android settings --> Password, default password service

![Screenshot of Android settings for passwords, passkeys and accounts. Preferred service is set to Keepass2Android, below there's a button to edit this. Below there's a list of additional services, Google is selected there with a checkbox set to true.](../android-passwords-passkeys.png)

