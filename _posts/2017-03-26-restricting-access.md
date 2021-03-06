---
layout: page
title: "Restricting access"
category: configuration
date: 2017-03-26 19:45:00
published: true
order: 3
summary: "Restrict access to the site in various ways"
---

> **Roll-out in progress:** These features are new and they might not be available in all environments yet.

## Restricting access with built-in features of WordPress

WordPress offers a multitude of user management and access control related features. We warmly recommend you use them as your primary means of access management. You can easily protect individual posts with a password. You can activate [Maintenance Mode](https://wordpress.org/plugins/maintenance/) and allow only selected users to access the entire site.

For more complex scenarios you can install BuddyPress or use some of the groups plugins.

Fiddling with Nginx based access control methods should be your last resort.

## Restricting access with HTTP Basic Authentication

The HTTP Authentication headers based system is quite old fashioned and not exactly the state-of-the art in cryptographic security. The list of usernames and passwords needs to be maintained manually using the `htpasswd` command line utility. First create a file with the `-c` option and then add more users as you need:

```
htpasswd -bc /data/wordpress/nginx/htpasswd-file-example username1 adminpassword
htpasswd -b /data/wordpress/nginx/htpasswd-file-example username2 userpassword
```

Once you have generated a htpasswd file you can activate it for a particular path by for example creating a file `/data/wordpress/nginx/htauth.conf` like this:

```
location ^~ /restricted-section/ {
  auth_basic "Arbitrary realm name";
  auth_basic_user_file /data/wordpress/nginx/htpasswd-file-example;
}
```

Remember to run `wp-restart-nginx` to make the new Nginx config file effective.

> **Warning:** Do not activate HTTP Authentication for the entire site. Otherwise you will render the `wp-test` test unusable, all automatic monitoring of your site will start to fail and Seravo's admins cannot access your site to check it and do upkeep anymore.

## Restricting access by IP address

If you have a section of your site that should be visible for example to visitors from a certain subnet, typically some sort of intranet or extranet page, you might want to use IP address based access controls. Be warned however that it is really hard to get right. Unlike domain names, IP addresses come and go, and you need to manually keep the IP lists up-to-date. IP addresses should not be used for very sensitive content, as per-user audit trail is formed using blanket IP access rules.

Sometimes your users need to access the page on-the-go, so you should also provide a password authenticated venue of access.

```
location ^~ /restricted-section/ {
  allow 1.2.3.4;
  allow 5.6.7.8;
  deny all;
}
```

> **Warning:** Do not activate IP address based restrictions for the entire site. Otherwise you will render the `wp-test` test unusable, all automatic monitoring of your site will start to fail and Seravo's admins cannot access your site to check it and do upkeep anymore.
