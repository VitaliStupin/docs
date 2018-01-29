# Kibana Setup and Configuration

## MongoDB server

Prepare MongoDB for data replication and setting the following lines in `/etc/mongod.conf` to:
```
replication:
  replSetName: rs0
  oplogSizeMB: 100
```

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
ExecStart=/usr/local/bin/mongo-connector -c /opt/riajenk/xtee-ci-xm/config.json

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl start mongo-connector-xtee-ci-xm.service
```

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
