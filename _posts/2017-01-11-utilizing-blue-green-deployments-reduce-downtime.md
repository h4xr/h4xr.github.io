---
id: 144
title: Utilizing Blue Green Deployments to Reduce Downtime
date: 2017-01-11T07:03:16+00:00
author: saurabh_badhwar
layout: post
guid: http://saurabhbadhwar.xyz/blog/?p=144
permalink: /2017/01/11/utilizing-blue-green-deployments-reduce-downtime/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:0:"";s:10:"author_url";s:24:"https://medium.com/@h4xr";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:3:"yes";s:2:"id";s:12:"54429a5a9112";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:89:"https://medium.com/@h4xr/utilizing-blue-green-deployments-to-reduce-downtime-54429a5a9112";}'
image: /wp-content/uploads/2017/01/Untitled-presentation.png
categories:
  - Uncategorized
tags:
  - Application Deployment
  - Blue Green Deployment
  - CD
  - CI
  - Reduced downtime
---
One of the aims while deploying an application from its final testing phase to production is to reduce the downtime. Another important aspect is to keep the risk low. Well, no one wants to serve their users with errors. To achieve the two objectives, developers and testers spend a good amount of time in testing their applications before getting them deployed into production. CI and CD systems help a lot in achieving the above two objectives. Rigorous testing procedures precede the application deployments. But, as a developer, most of us are aware of the fact that no matter how rigorously we test the applications, there are some issues which unintentionally reaches into production and gets discovered only when users are actually utilizing that application. The result of this may range from some small operational bugs to application failure causing disruption of service.

In this situation, blue green deployments can help a lot. Mainly they can help us in reducing the downtime and the risk associated with pushing an update into production. But the question that arises is, what are blue-green deployments? Let&#8217;s take a look

### Blue Green Deployments

Blue Green deployment is a release technique which aims at reducing the downtime associated with the release of an application into production. How this is achieved is by the fact that, in blue green deployments, we try to maintain two production environments which are identical to each other. Now, at any particular instant of time, only one of these production environments is live. The live environment is the one which is responsible for serving all the requests.

<img class="size-full wp-image-145" src="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2017/01/Untitled-presentation.png?fit=640%2C360" alt="Blue Green Deployment" srcset="https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2017/01/Untitled-presentation.png?w=960 960w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2017/01/Untitled-presentation.png?resize=300%2C169 300w, https://i2.wp.com/saurabhbadhwar.xyz/blog/wp-content/uploads/2017/01/Untitled-presentation.png?resize=768%2C432 768w" sizes="(max-width: 640px) 100vw, 640px" data-recalc-dims="1" /> 

Effectively, what we have here is that, two production environments, one which contains the previous release (Blue) and another which contains the new release (Green).

Now, lets try to understand, how the whole thing works. Imagine you have an application running in production (Blue) and now we want to deploy an updated version of the same application into production. In Blue Green deployment, we now create an identical production environment (Green) and push the updated application code to that environment. Now, we setup our router to send the incoming requests to both the environments. Slowly, we configure our router to send the requests to the new production environment. If everything works fine, we can discard the old production environment (Blue one). This cycle repeats for the upcoming releases.

#### The advantages

So, what are the advantages here? The first and foremost is that, the technique provides a mechanism for instant rollback. In case, our new application showed some unexpected behavior, we can configure our router to reroute the requests to our previous production environment until we fix the new one. Yes, there might be a very small downtime while the router is configured, but it is still very less as compared to the one an application failure may generate.

Another advantage here is that, while utilizing blue green deployments, we managed to deploy our new codebase without taking the application down.

So, to conclude, we can say that, using blue green deployments, we can reduce our downtime as well as reduce the risk factor associated with the deployment of an updated codebase to the production.

For further reading, I will recommend this excellent <a href="https://martinfowler.com/bliki/BlueGreenDeployment.html" target="_blank">article by Martin Fowler</a> on Blue Green Deployments.