# AWS IoT setup with Cloudformation

An AWS IoT setup from scratch with Cloudformation.


### Initial setup

__This should only be run once!__

```bash
# Create the base API setup
$ bash ./bin/create-stack.sh --template base

# Create new iot groups
$ bash ./bin/create-stack.sh --template iam-groups

# Create new iot users
$ bash ./bin/create-stack.sh --template iam-users

```


### Update stack

After every change you can run this command to update the stack.

```bash
# Update the base API setup
$ bash ./bin/create-stack.sh --template base --update-stack

# Update iot groups
$ bash ./bin/create-stack.sh --template iam-groups

# Update iot users
$ bash ./bin/create-stack.sh --template iam-users

```

### Set Elasticsearch index template

Make sure index is deleted.
```bash
$ export CF_IOT_ES_DOMAIN_URL=$(aws cloudformation describe-stacks --stack-name iot-rules --profile ffe | jq '.Stacks[].Outputs[] | { OutputKey: "iotElasticsearchURL", OutputValue: .OutputValue }' | jq -r '.OutputValue')

$ curl -XDELETE "${CF_IOT_ES_DOMAIN_URL}/iot-nodemcu"
```

Create new index with mappings.
```bash
$ curl -i -X PUT -d '{
  "mappings": {
    "sensor-data": {
      "properties": {
        "timestamp": {
          "type": "long",
          "copy_to": "datetime"
        },
        "datetime": {
          "type": "date",
          "store": true
        },
        "location": {
          "type": "geo_point"
        }
      }
    }
  }
}' "${CF_IOT_ES_DOMAIN_URL}/iot-nodemcu"
```


## Other Resources

* [AWS Basic setup with Cloudformation](https://github.com/5orenso/aws-cloudformation-base)
* [AWS Lambda boilerplate](https://github.com/5orenso/aws-lambda-boilerplate)
* [Automated AWS Lambda update](https://github.com/5orenso/aws-lambda-autodeploy-lambda)
* [AWS API Gateway setup with Cloudformation](https://github.com/5orenso/aws-cloudformation-api-gateway)
* [AWS IoT setup with Cloudformation](https://github.com/5orenso/aws-cloudformation-iot)
