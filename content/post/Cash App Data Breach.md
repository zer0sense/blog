---
title: "Cash App Data Breach"
date: 2022-04-13T19:16:22+09:00
description: "Cash App Data Breach"
tags: [Data Breach]
categories: [News]
comments: false
draft: false
---

My blog, so far, has been covering a lot of technical articles. I wanted to try my hand with a new type of article. During my ingestion of cyber security news I came across the [Cash App Data Breach](https://thehackernews.com/2022/04/block-admits-data-breach-involving-cash.html). This peaked my interest due to the way they were breached, and that is going to be the main topic of this blog post. The breach was said to be caused by a former employee accessing the system after their employment ended with the company. A few scenarios came to mind, and since my initial look into the news about this didn't reveal the exact way the employee utilized the system I thought why not cover a couple of scenarios where I can see this happening.

### Not Disabling Users Accounts

Offboarding processes. When an employee leaves your company there should be a type of checklist or defining process that is validated when they leave. With the amount of people working from home, again I am not stating this was the case, and the remote access that was rushed due to adapt to the COVID and Post-Covid workspace remote access to company networks became more of a necessity. That might add complexity to tracking when employees are no longer with the company, as the VPN authentication should be immediately disabled.

Is there a company web portal or web application that has it's own authentication database? You don't need to deploy and maintain a VPN server if you can have a web app or web portal for the employees to do their duties from the office or from remote locations, right? If that is your risk appetite then yes, but you should definitely have your offboarding process reviewed by multiple departments to have a better chance of success. When HR is processing an employee leaving the company, are they sending communications to people like your IT staff and managers? Are those IT staff and managers communicating back to HR that that they have done their part of the offboarding process? When was the last time the IT portion of offboarding was reviewed? Have you deployed new servers, web apps, and other technologies since the original offboarding process was created? Can employees access those new technologies without being offsite? These are questions that should come up and addressed frequently because businesses evolve and so do the technologies that support them. 

This is a scenario that has little to no cost or budget requests to remedy. Set solid processes, review those processes regularly, and make sure the communication channel is flowing between departments. 


### Group Accounts and Password Policies

Passwords are a pain! I know, and I know what you're thinking. What would this have to do with how this data breach occurred? Well, does the organization you work for have group or service accounts? Maybe an admin account that can unlock individual accounts when their passwords expire or are locked out. Not unreasonable, right? The last thing you want is to have the payroll person that is processing your direct deposit not able to do their duties because you can't unlock their account due to caps lock being on and being locked out yourself. How is that group admin account or service account being managed? Is there a complicated password for that account? Is that complicated password then stored centrally? Is that centrally stored document password protected or read/write limited? Can employees that get curious and see that there are more network shares out there that they can access and possible view these precious documents? How often do you change this group admin or server account's password? This scenario becomes a scary reality when you have a solid process for individual accounts, and your company is offboarding correctly. Sure, that former employee cant access their own account to access company resources, but they have been using that group admin account for so long the password is committed to memory. Therefore, your public web app with access to your companies resources is still wide open, and if this account was accessed by a former employee will you be alerted? Honestly, a successful login to a group account would probably look like a non-security related issue. 

With this scenario, if you absolutely need a group admin account change the password frequently. Have a complicated password, and change it regularly. Protect the access to that password with the highest amount of security controls you can. 


## Outsourcing Your IT

This scenario is going to be a short one. The main question is if you have a MSP managing your IT; are they notified when employees leave the company. Can a former employee contact this MSP's and ask for a password reset? This comes down to a lot of the same questions that was brought up in the Not Disabling Accounts scenario. However, the emphasis I would like to address with this scenario is how is this communicated to your outsourced IT department? 


## My Last 2cents

I hope you find this article well. Take the time and reflect on how you can improve your offboarding process at your own company. You don't need to be an IT director, HR specialist, or C suite executive to think about the processes in place and see if they are effective. Maybe you are the IT technician that gets tasked with auditing active user accounts, or you are processing and employee that is leaving and you see room for improvement. You can make an impact to prevent an ex employee, that might be vindictive, and keep your companies data from being breached.
