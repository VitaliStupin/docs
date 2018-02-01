# Kibana Setup and Configuration

## MongoDB server

Prepare MongoDB for data replication and setting the following lines in `/etc/mongod.conf` to:
```
replication:
  replSetName: rs0
  oplogSizeMB: 100
```

Note that oplogSizeMB must be big enough so that syncronization issues could be fixed before older items in oplog are rewriten
by newer ones.

Restart MongoDB server:
```
service mongod restart
```

Use MongoDB shell (`mongo`) to initialize replication:
```
rs.initiate()
```

Check that oplog is being collected:
```
rs.printReplicationInfo()
```

From MongoDB shell create a user with sufficient rihts to query replication data from MongoDB. You need to add "read" role
for "local" database to read oplog data and "read" roles for all databases that need to be replicated:
```
use admin
db.createUser({user: "xtee-ci-xm-replica", pwd: "PasswordForOplogger", roles: [{role: "read", db: "local"}, {role: "read", db: "query_db_XTEE-CI-XM"}]})
```

## Kibana server

### Install Kibana and Elasticsearch

Download and install the public signing key:
```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```

Add APT repository definition:
```
echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
```

Install Kibana and Elasticsearch:
```
sudo apt-get update && sudo apt-get install kibana elasticsearch
```

Install PIP:
```
sudo apt-get install python-pip
```

Install mongo-connector:
```
sudo pip install mongo-connector elastic2-doc-manager elasticsearch
```

Create template for "opmon*" indexes in Elasticsearch:
```
 curl -XPUT 'http://localhost:9200/_template/opmon?pretty' -H 'Content-Type: application/json' -d'
{
  "index_patterns": ["opmon*"],
  "mappings" : {
    "clean_data" : {
      "properties" : {
        "startTs" : {
          "type" : "date",
          "format" : "yyyy-MM-dd HH:mm:ss.SSS||epoch_millis"
        },
        "consumerMember" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "consumerSystem" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "producerMember" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "producerSystem" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "service" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "succeeded" : {
          "type" : "boolean"
        },
        "valid" : {
          "type" : "boolean"
        },
        "messageUserId" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "client" : {
          "properties" : {
            "_id" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "clientMemberClass" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "clientMemberCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "clientSecurityServerAddress" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "clientSubsystemCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "clientXRoadInstance" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "insertTime" : {
              "type" : "date",
              "format" : "yyyy-MM-dd HH:mm:ss||epoch_second"
            },
            "messageId" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "messageIssue" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "messageProtocolVersion" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "messageUserId" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "monitoringDataTs" : {
              "type" : "date",
              "format" : "yyyy-MM-dd HH:mm:ss||epoch_second"
            },
            "representedPartyClass" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "representedPartyCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "requestAttachmentCount" : {
              "type" : "long"
            },
            "requestInTs" : {
              "type" : "date",
              "format" : "yyyy-MM-dd HH:mm:ss.SSS||epoch_millis"
            },
            "requestMimeSize" : {
              "type" : "long"
            },
            "requestOutTs" : {
              "type" : "date",
              "format" : "yyyy-MM-dd HH:mm:ss.SSS||epoch_millis"
            },
            "requestSoapSize" : {
              "type" : "long"
            },
            "responseAttachmentCount" : {
              "type" : "long"
            },
            "responseInTs" : {
              "type" : "date",
              "format" : "yyyy-MM-dd HH:mm:ss.SSS||epoch_millis"
            },
            "responseMimeSize" : {
              "type" : "long"
            },
            "responseOutTs" : {
              "type" : "date",
              "format" : "yyyy-MM-dd HH:mm:ss.SSS||epoch_millis"
            },
            "responseSoapSize" : {
              "type" : "long"
            },
            "securityServerInternalIp" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "securityServerType" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceMemberClass" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceMemberCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceSecurityServerAddress" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceSubsystemCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceVersion" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceXRoadInstance" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "soapFaultCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "soapFaultString" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "succeeded" : {
              "type" : "boolean"
            }
          }
        },
        "clientHash" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "clientRequestSize" : {
          "type" : "long"
        },
        "clientResponseSize" : {
          "type" : "long"
        },
        "clientSsRequestDuration" : {
          "type" : "long"
        },
        "clientSsResponseDuration" : {
          "type" : "long"
        },
        "correctorStatus" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "correctorTime" : {
          "type" : "date",
          "format" : "yyyy-MM-dd HH:mm:ss||epoch_second"
        },
        "matchingType" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "messageId" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "producer" : {
          "properties" : {
            "_id" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "clientMemberClass" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "clientMemberCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "clientSecurityServerAddress" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "clientSubsystemCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "clientXRoadInstance" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "insertTime" : {
              "type" : "date",
              "format" : "yyyy-MM-dd HH:mm:ss||epoch_second"
            },
            "messageId" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "messageIssue" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "messageProtocolVersion" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "messageUserId" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "monitoringDataTs" : {
              "type" : "date",
              "format" : "yyyy-MM-dd HH:mm:ss||epoch_second"
            },
            "representedPartyClass" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "representedPartyCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "requestAttachmentCount" : {
              "type" : "long"
            },
            "requestInTs" : {
              "type" : "date",
              "format" : "yyyy-MM-dd HH:mm:ss.SSS||epoch_millis"
            },
            "requestMimeSize" : {
              "type" : "long"
            },
            "requestOutTs" : {
              "type" : "date",
              "format" : "yyyy-MM-dd HH:mm:ss.SSS||epoch_millis"
            },
            "requestSoapSize" : {
              "type" : "long"
            },
            "responseAttachmentCount" : {
              "type" : "long"
            },
            "responseInTs" : {
              "type" : "date",
              "format" : "yyyy-MM-dd HH:mm:ss.SSS||epoch_millis"
            },
            "responseMimeSize" : {
              "type" : "long"
            },
            "responseOutTs" : {
              "type" : "date",
              "format" : "yyyy-MM-dd HH:mm:ss.SSS||epoch_millis"
            },
            "responseSoapSize" : {
              "type" : "long"
            },
            "securityServerInternalIp" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "securityServerType" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceMemberClass" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceMemberCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceSecurityServerAddress" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceSubsystemCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceVersion" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "serviceXRoadInstance" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "soapFaultCode" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "soapFaultString" : {
              "type" : "text",
              "fields" : {
                "keyword" : {
                  "type" : "keyword",
                  "ignore_above" : 256
                }
              }
            },
            "succeeded" : {
              "type" : "boolean"
            }
          }
        },
        "producerDurationClientView" : {
          "type" : "long"
        },
        "producerDurationProducerView" : {
          "type" : "long"
        },
        "producerHash" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "producerIsDuration" : {
          "type" : "long"
        },
        "producerRequestSize" : {
          "type" : "long"
        },
        "producerResponseSize" : {
          "type" : "long"
        },
        "producerSsRequestDuration" : {
          "type" : "long"
        },
        "producerSsResponseDuration" : {
          "type" : "long"
        },
        "requestNwDuration" : {
          "type" : "long"
        },
        "responseNwDuration" : {
          "type" : "long"
        },
        "totalDuration" : {
          "type" : "long"
        }
      }
    }
  }
}
'
```

Configure mongo-connector:
```
mkdir /opt/riajenk/xtee-ci-xm
 cat > /opt/riajenk/xtee-ci-xm/config.json <<EOF
{
    "mainAddress": "opmon.ci.kit:27017",
    "authentication": {
        "adminUsername": "xtee-ci-xm-replica",
        "password": "PasswordForOplogger"
    },
    "namespaces": {
        "query_db_XTEE-CI-XM.clean_data": "opmon-xtee-ci-xm.clean_data"
    },
    "docManagers": [
        {
            "docManager": "elastic2_doc_manager",
            "targetURL": "localhost:9200"
        }
    ]
}
EOF

 sudo bash -c "cat > /lib/systemd/system/mongo-connector-xtee-ci-xm.service" <<EOF
[Unit]
Description=Mongo-connector service for xtee-ci-xm
After=multi-user.target elasticsearch.service

[Service]
User=riajenk
Restart=always
RestartSec=10
WorkingDirectory=/opt/riajenk/xtee-ci-xm
ExecStartPre=/bin/sleep 30
ExecStart=/usr/local/bin/mongo-connector -c /opt/riajenk/xtee-ci-xm/config.json

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl start mongo-connector-xtee-ci-xm.service
```

Note that mongo-connector will sleep for 30 seconds before starting to make sure that elasticsearch service has started properly.

Start mongo-connector daemon:
```
sudo systemctl daemon-reload
sudo systemctl start mongo-connector-xtee-ci-xm.service
```

Add a script for data partitioning:
```
 cat > /opt/riajenk/partition.py <<EOF
#!/usr/bin/python

from elasticsearch import Elasticsearch
from elasticsearch import helpers
import argparse
import time


SIZE = 5000


parser = argparse.ArgumentParser(
    description='Partition OPMON index in elasticsearch.'
)
parser.add_argument('index', metavar='INDEX_NAME', help='Source index to partition.')
args = parser.parse_args()

es = Elasticsearch()

page = es.search(index=args.index, size=SIZE, scroll = '2m', q='correctorStatus: done')

sid = page['_scroll_id']
scroll_size = len(page['hits']['hits'])
print('Total items to process: {}'.format(page['hits']['total']))

while (scroll_size > 0):
    actions = []
    del_actions = []

    print('Processing {} items'.format(scroll_size))

    for hit in page['hits']['hits']:
        if 'client' in hit['_source'] and hit['_source']['client'] is not None and 'requestInTs' in hit['_source']['client']:
            data = hit['_source']['client']
        elif 'producer' in hit['_source'] and hit['_source']['producer'] is not None and 'requestInTs' in hit['_source']['producer']:
            data = hit['_source']['producer']
        else:
            print('Both client.requestInTs and producer.requestInTs are missing! ID={}'.format(hit['_id']))
            continue

        doc = hit['_source']
        doc['startTs'] = data['requestInTs']
        doc['consumerMember'] = '/'.join([data['clientXRoadInstance'] or '', data['clientMemberClass'] or '', data['clientMemberCode'] or ''])
        doc['consumerSystem'] = '/'.join([data['clientXRoadInstance'] or '', data['clientMemberClass'] or '', data['clientMemberCode'] or '', data['clientSubsystemCode'] or ''])
        doc['producerMember'] = '/'.join([data['serviceXRoadInstance'] or '', data['serviceMemberClass'] or '', data['serviceMemberCode'] or ''])
        doc['producerSystem'] = '/'.join([data['serviceXRoadInstance'] or '', data['serviceMemberClass'] or '', data['serviceMemberCode'] or '', data['serviceSubsystemCode'] or ''])
        doc['service'] = '/'.join([data['serviceXRoadInstance'] or '', data['serviceMemberClass'] or '', data['serviceMemberCode'] or '',
            data['serviceSubsystemCode'] or '', data['serviceCode'] or '', data['serviceVersion'] or ''])
        doc['succeeded'] = data['succeeded']
        doc['valid'] = True if data['serviceXRoadInstance'] else False
        doc['messageUserId'] = data['messageUserId']

        part = time.strftime('%Y-%m', time.gmtime(data['requestInTs']/1000))

        action = {
            '_op_type': 'update',
            '_index': '{}-{}'.format(args.index, part),
            '_type': 'clean_data',
            '_id': hit['_id'],
            'doc_as_upsert': True,
            'doc': doc
        }
        actions.append(action)

        action = {
            '_op_type': 'delete',
            '_index': args.index,
            '_type': 'clean_data',
            '_id': hit['_id']
        }
        del_actions.append(action)
    
    bulk_res = helpers.bulk(es, actions, stats_only=True)
    if bulk_res[1] == 0:
        print('Added/updated to partitioned indexes: {}'.format(bulk_res[0]))
        bulk_res = helpers.bulk(es, del_actions, stats_only=True)
        print('Removed from source index: {}'.format(bulk_res[0]))
    else:
        print('Failed to add/update: {}'.format(bulk_res[1]))

    page = es.scroll(scroll_id = sid, scroll = '2m')
    sid = page['_scroll_id']
    scroll_size = len(page['hits']['hits'])
EOF
chmod a+x /opt/riajenk/partition.py
```

And configure crontab by running:
```
crontab -e
```

And adding the following line:
```
* * * * * flock -xn /opt/riajenk/xtee-ci-xm/partition.lock -c "/opt/riajenk/partition.py opmon-xtee-ci-xm"
```

## Known issues

https://github.com/mongodb-labs/mongo-connector/issues/791

If Logstash is offline (rejects connections) while mongo-connector tries to replicate data from MongoDB,
then it is possible that oplog.timestamp is updated while data is not replicated. The following lines should appear
in mongo-connector.log:

```
2018-01-31 14:33:58,550 [WARNING] elasticsearch:97 - GET http://localhost:9200/_mget?realtime=true [status:N/A request:0.000s]
Traceback (most recent call last):
...
  File "/usr/local/lib/python2.7/dist-packages/urllib3/connection.py", line 150, in _new_conn
    self, "Failed to establish a new connection: %s" % e)
NewConnectionError: <urllib3.connection.HTTPConnection object at 0x7f54fb59f7d0>: Failed to establish a new connection: [Errno 111] Connection refused
```

After that mongo-connector service would apper as running while actually no data is being replicated.

## Problem debuging and solving

**Checking if oplog is big enough:**

In MongoDB:
```
db.getSiblingDB("local").oplog.rs.find().sort( {"ts": 1} ).limit(1)
```

Example output:
```
"ts" : Timestamp(1514359228, 1)
```

This epoch time shold be at least week old, which would give you at least one week to fix any synchronization problems.

**Check if oplog.timestamp if up to date:**

In server that has mongo-connector installed:
```
$ cat /opt/riajenk/xtee-ci-xm/oplog.timestamp
["rs0", 6517184251123728386]
$ python -c 'from mongo_connector.util import long_to_bson_ts; print long_to_bson_ts(6517184251123728386)'
Timestamp(1517400204, 2)
```

Note that mongo-connector does not update oplog.timestamp in real time, so you might need to stop mongo-connector
before checking oplog.timestamp

The reverse convertion:
```
python -c 'from bson.timestamp import Timestamp; from mongo_connector.util import bson_ts_to_long; print bson_ts_to_long(Timestamp(1517400204, 2))'
```

Additionally it is possible to query oplog to see the last synchronized item (in MongoDB shell):

```
rs0:PRIMARY> db.getSiblingDB("local").oplog.rs.find( {"ts": Timestamp(1517400204, 2)} )
{ "ts" : Timestamp(1517400204, 2), "t" : NumberLong(3), "h" : NumberLong("-7189340831937961635"), "v" : 2, "op" : "u", "ns" : "query_db_XTEE-CI-XM.clean_data", "o2" : { "_id" : ObjectId("5a688459612d52480b8999e2") }, "o" : { "$set" : { "correctorTime" : 1517400204.2664201, "correctorStatus" : "done" } } }

```

**To make sure all data was transfered and partitioned from MongoDB to Elasticsearch execute the following commands:**

In MongoDB:
```
db.clean_data.find({ correctorStatus: {$not: /done/} }).count()
```

Example output: `2773`

In Elasticsearch server:
```
curl http://localhost:9200/opmon-xtee-ci-xm/_stats/docs?pretty
```

Example output:
```
...
  "indices" : {
    "opmon-xtee-ci-xm" : {
      "primaries" : {
        "docs" : {
          "count" : 2773,
...
```

**To correct syncronization issue (data is out of sync while oplog.timestamp is up to date / oplog.timestamp is corrupted):**
Reapplying oplog:
```
sudo systemctl stop mongo-connector-xtee-ci-xm
/opt/riajenk/xtee-ci-xm
mv oplog.timestamp oplog.timestamp_bak
/usr/local/bin/mongo-connector -c config.json --no-dump
```
Wait untill syncronisation completes (compare counts in MongoDB and Elasticsearch), then stop mongo-connector
with keyboard combination "ctrl + c".

Then you can start the service again:
```
sudo systemctl stop mongo-connector-xtee-ci-xm
```

Note that this will try to replay oplog without dumping the full database, and therefore this method can be used only if
syncronization issue is recent enough.

Alternatively it is possible to replay oplog partially. If you know that data syncronisation was working properly up to
sertain time you can select time before last data was received and calculate a new value for oplog.timestamp.

In the following example we assume that there were successful replications after epoch time "1517389200".

First we find in MongoDB one of the oplog items that was already successfully replicated:
```
rs0:PRIMARY> db.getSiblingDB("local").oplog.rs.find( {"ts": {$gt: Timestamp(1517389200, 1)}} ).sort({"ts": 1}).limit(1)
{ "ts" : Timestamp(1517389210, 1), "t" : NumberLong(3), "h" : NumberLong("-5524383954901817504"), "v" : 2, "op" : "n", "ns" : "", "o" : { "msg" : "periodic noop" } }
```

Then we calculate a valid value for oplog.timestamp (in mongo-connector server):
```
$ python -c 'from bson.timestamp import Timestamp; from mongo_connector.util import bson_ts_to_long; print bson_ts_to_long(Timestamp(1517389210, 1))'
6517137032253276161
```

Then we stop the service:
```
sudo systemctl stop mongo-connector-xtee-ci-xm
```

Update oplog.timestamp with new value "6517137032253276161"

And start the service again:
```
sudo systemctl start mongo-connector-xtee-ci-xm
```
