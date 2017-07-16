---
layout: post
title: "Modifying a Redshift table"
date: 2017-05-23 12:49:50 +0800
comments: true
categories: 
---

    CREATE TABLE conversion (
      id             VARCHAR(36) PRIMARY KEY NOT NULL,
      visit_id       VARCHAR(36) NOT NULL,
      amount         NUMERIC(11,2) DEFAULT 0.00,
      txid           VARCHAR(64)  NOT NULL,
      timestamp      TIMESTAMP NOT NULL DEFAULT GETDATE()
    )

    DISTSTYLE KEY
    DISTKEY (timestamp)
    COMPOUND SORTKEY(visit_id, timestamp);

Problem #1: How to remove the NOT NULL constraint in timestamp column?

    ALTER TABLE conversion ADD COLUMN timestamp2 TIMESTAMP DEFAULT GETDATE();
    UPDATE conversion SET timestamp2 = timestamp;


    mldb=# ALTER TABLE conversion DROP COLUMN timestamp;
    ERROR:  cannot drop distkey column "timestamp"

