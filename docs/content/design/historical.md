<!--
  ~ Licensed to the Apache Software Foundation (ASF) under one
  ~ or more contributor license agreements.  See the NOTICE file
  ~ distributed with this work for additional information
  ~ regarding copyright ownership.  The ASF licenses this file
  ~ to you under the Apache License, Version 2.0 (the
  ~ "License"); you may not use this file except in compliance
  ~ with the License.  You may obtain a copy of the License at
  ~
  ~   http://www.apache.org/licenses/LICENSE-2.0
  ~
  ~ Unless required by applicable law or agreed to in writing,
  ~ software distributed under the License is distributed on an
  ~ "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
  ~ KIND, either express or implied.  See the License for the
  ~ specific language governing permissions and limitations
  ~ under the License.
  -->

---
layout: doc_page
title: "Historical Node"
---
# Historical Node

### Configuration

For Historical Node Configuration, see [Historical Configuration](../configuration/index.html#historical).

### HTTP Endpoints

For a list of API endpoints supported by the Historical, please see the [API reference](../operations/api-reference.html#historical).

### Running

```
org.apache.druid.cli.Main server historical
```

### Loading and Serving Segments

Each historical node maintains a constant connection to Zookeeper and watches a configurable set of Zookeeper paths for new segment information. Historical nodes do not communicate directly with each other or with the coordinator nodes but instead rely on Zookeeper for coordination.

The [Coordinator](../design/coordinator.html) node is responsible for assigning new segments to historical nodes. Assignment is done by creating an ephemeral Zookeeper entry under a load queue path associated with a historical node. For more information on how the coordinator assigns segments to historical nodes, please see [Coordinator](../design/coordinator.html).

When a historical node notices a new load queue entry in its load queue path, it will first check a local disk directory (cache) for the information about segment. If no information about the segment exists in the cache, the historical node will download metadata about the new segment to serve from Zookeeper. This metadata includes specifications about where the segment is located in deep storage and about how to decompress and process the segment. For more information about segment metadata and Druid segments in general, please see [Segments](../design/segments.html). Once a historical node completes processing a segment, the segment is announced in Zookeeper under a served segments path associated with the node. At this point, the segment is available for querying.

### Loading and Serving Segments From Cache

Recall that when a historical node notices a new segment entry in its load queue path, the historical node first checks a configurable cache directory on its local disk to see if the segment had been previously downloaded. If a local cache entry already exists, the historical node will directly read the segment binary files from disk and load the segment.

The segment cache is also leveraged when a historical node is first started. On startup, a historical node will search through its cache directory and immediately load and serve all segments that are found. This feature allows historical nodes to be queried as soon they come online.

### Querying Segments

Please see [Querying](../querying/querying.html) for more information on querying historical nodes.

A historical can be configured to log and report metrics for every query it services.
