---
layout: post
title: "Remove duplicates from millions of records with Python Dedupe Library"
description: "This post describes the learnings I had when I built a pipeline that dedupelicate millions of database records.
             Find out how I designed it and maybe you can pick up some tips and apply it to your dedupe projects."
---

Earlier this year 2022. I had the opportunity to solve an interesting problem in my previous employer.
One of the upcoming SaaS product of the company is selling sales leads which can be used in targeted email campaigns etc.

Few months ago they had built an web scraper (miner) that scrapes website informations non-stop 24/7.
Fast forward and it had already scraped around 16 million database records of website leads. The problem is that the
database records contain duplicates or otherwise "dirty" data. One of possible solution we have explored is the Dedupe library in Python.

[**dedupe**][dedupe] is a library that uses machine learning to perform de-duplication and entity resolution quickly on structured data.

If you're curious how the dedupe works check their [docs][how-it-works]. I will not dive into details of how to implement it step by step
but I will be reflecting on the learnings that I made during the implementation. The deduper library is memory intensive (RAM) so if your
script is always freezes or just hangs, check your system RAM if it maxed out, give it more RAM and restart. See their [troubleshooting][troubleshoot]
guide.

If your data is in millions better to use some form of server side cursors for querying the database so the record is oly fetched on demand,
in our case our data is in Postgresql so this is not a problem.

Initialy the management wants/insists our deduper script to be run on our office desktop computer with big RAM to save costs vs hosting on AWS EC2.
As per my estimates on actual test runs for our 16 millions records the time of run completion will be more than 1 month which is not acceptable.
Upon closer inspection, the SQL queries being performed by the deduper is the culprit, the sql queries are so slow.
Since the deduper script is being run on premise with slow networking it results in slow sql queries. This convinced the management to host the
deduper script in AWS EC2 instance with big RAM.

As per my observation, the deduper RAM consumption is based on the dataset it is operating on. The more the duplicates are in the records,
the more RAM it will consume. So this will always depend on a case by case basis.

Looking at their [official docs][pg-example], the sample postgres based example only has 700,000 records which may fit into RAM/memory.
But in our case we have 16 million records. The deduper is not yet desinged to handle million of records out of the box. More work should be
done by the developer implementing the deduper to fit into their requirements.

Surely 16 million records won't fit in RAM memory, what I did was that I setup the deduper to dedupe by batch of X records at a time.
That X records will be dependent on your particular dataset and you have to figure out that optimal number with respect to the amount of RAM
where your deduper script will run ie. EC2.

In our case, the optimal number is 10,000 records per run. The worst case is that all 10,000 records are being considered by dedupe as duplicates
the RAM consumption of EC2 maxed out at 64 GB and the system will hang/stop. In this scenario, we can either increase the RAM capacity 64GB to 128GB
will will also significantly double your ec2 bill or halve the X optimal number from 10,000 records to 5,000 records. We move forward with the latter
approach to minimize resource bill. Your mileage may vary though.

As per my rough estimates, at 10,000 records per run we will have a total of 1,600 runs to completion of 16 million records.
Doubling the optimal number at 20,000 records per run will have a total of 800 runs but will also require doubling the amount of RAM at 128 GB to cover the
worst case and will also double the aws cost.

Also at 10,000 records the completion time is at 14 days, doubling the optimal record at 20,000 will halve the completion time to just 7 days.

In the end we are quite happy with the results, the dedupe library proved to be a valuable tool in your arsenal.
We have deduped the 16 million records into around 7 million uniques that it can finally sell to users.

[dedupe]: https://docs.dedupe.io/en/latest/
[pg-example]: https://github.com/dedupeio/dedupe-examples/blob/master/pgsql_big_dedupe_example/pgsql_big_dedupe_example.py
[how-it-works]: https://docs.dedupe.io/en/latest/how-it-works/How-it-works.html
[troubleshoot]: https://docs.dedupe.io/en/latest/Troubleshooting.html
