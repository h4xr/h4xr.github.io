---
id: 46
title: From development to deployment
date: 2016-05-14T13:26:59+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=46
permalink: /2016/05/14/from-development-to-deployment/
categories:
  - Internship
tags:
  - cache
  - cloudflare
  - deployment
  - django
  - internship
  - linode
  - pristine cut
  - python
---
Development only forms one part of a project, and a project is never complete until it is deployed and running properly. This post summarizes my experience on working on a project for my Internship which required us to improvise on a live site and make those improvisations deployed to the site.

My internship at <a href="http://www.pristinecut.com" target="_blank">Pristine Cut</a> along with my fellow teammates comprises of Backend development work in which we are improvising upon the administration of the whole platform as well as implementing new techniques through which the user can interact with the platform.

Currently the platform is powered by the Django web framework to which the team is new. With the course of time we are learning more and more about the framework as well as integrating new features into the platform side by side. What seemed to be an easy to achieve task could turn out to be so challenging was something the team never imagined. A simple change in our code caused the new customer booking to be disabled which forms the core part of the business. As soon as we got notified about the bug, the team was quickly able to fix the code in matter of minutes, but, the real challenge appeared when we deployed the fix to the production server.

Even after deploying the code, the button still didn&#8217;t worked. But, the same was working on our local machines without any issues. After spending some time debugging the cause, it seemed to be a simple caching issue where the Javascript file was cached by the web browser. So, the first course of action was to clear the browser cache and to try the site once again. Now, even after clearing the browser cache, the problem was still persistent. Now, when we looked at the requests, we noticed that the same file with the buggy code was still being served by the production server. Now, we started to relook at the issue from the server side. The course of action consisted of rebuilding the static file cache, restarting the nginx, invalidating the server caches, but, none of this helped. Now, this problem was getting more and more confusing. We started to evaluate every possible cause, from the static file server to local system caches. After at least an hour of hunting the cause, we noticed something which we were accidentally overlooking for the whole time. The platform uses Cloudflare for the live platform. This thing finally made the bulb glow that it could be Cloudflare cache that might be causing the problem. Now that we have identified the probable cause, it was time to find the remedy, we had to invalidate the caches stored on Cloudflare. Well, our identification and remedy didn&#8217;t went into waste this time. The new code was finally being delivered and the customer booking was up again.

The lessons learned, never deploy to a production server during the work hours and to look at the smallest of things while debugging issues.