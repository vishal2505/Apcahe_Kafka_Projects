# How to write Custom Connector

## Download Kafka Maven Archetype

[https://github.com/jcustenborder/kafka-connect-archtype]

#### File -> New project -> Maven -> SDK 1.8 -> Create from Archetype -> Fill Group id, Archetype id, version -> Create project providing custom grpup, artifact and version

```
mvn archetype:generate \
    -DarchetypeGroupId=com.github.jcustenborder.kafka.connect \
    -DarchetypeArtifactId=kafka-connect-quickstart \
    -DarchetypeVersion=2.0.0-cp1
```

Source Code - [https://github.com/simplesteph/kafka-connect-github-source/tree/v1.1]

Github Issues API - [https://developer.github.com/v3/issues/#list-issues-for-a-repository]

## Config Definitions

```
public static ConfigDef conf() {
        return new ConfigDef()
                .define(TOPIC_CONFIG, Type.STRING, Importance.HIGH, TOPIC_DOC)
                .define(OWNER_CONFIG, Type.STRING, Importance.HIGH, OWNER_DOC)
                .define(REPO_CONFIG, Type.STRING, Importance.HIGH, REPO_DOC)
                .define(BATCH_SIZE_CONFIG, Type.INT, 100, new BatchSizeValidator(), Importance.LOW, BATCH_SIZE_DOC)
                .define(SINCE_CONFIG, Type.STRING, ZonedDateTime.now().minusYears(1).toInstant().toString(),
                        new TimestampValidator(), Importance.HIGH, SINCE_DOC)
                .define(AUTH_USERNAME_CONFIG, Type.STRING, "", Importance.HIGH, AUTH_USERNAME_DOC)
                .define(AUTH_PASSWORD_CONFIG, Type.PASSWORD, "", Importance.HIGH, AUTH_PASSWORD_DOC);
    }

```

## Connector Class

```
 @Override
    public void start(Map<String, String> map) {
        config = new GitHubSourceConnectorConfig(map);
    }

    @Override
    public Class<? extends Task> taskClass() {
        return GitHubSourceTask.class;
    }

    @Override
    public List<Map<String, String>> taskConfigs(int i) {
        // Define the individual task configurations that will be executed.
        ArrayList<Map<String, String>> configs = new ArrayList<>(1);
        configs.add(config.originalsStrings());
        return configs;
    }

    @Override
    public ConfigDef config() {
        return GitHubSourceConnectorConfig.conf();
    }

```

## Writing a Schema

```
    // Key Schema
    public static final Schema KEY_SCHEMA = SchemaBuilder.struct().name(SCHEMA_KEY)
            .version(1)
            .field(OWNER_FIELD, Schema.STRING_SCHEMA)
            .field(REPOSITORY_FIELD, Schema.STRING_SCHEMA)
            .field(NUMBER_FIELD, Schema.INT32_SCHEMA)
            .build();
```

## Data Models for the objects

## Writing Github API http Client

[https://github.com/simplesteph/kafka-connect-github-source/blob/master/src/main/java/com/simplesteph/kafka/GitHubAPIHttpClient.java]

## Source Partition and source offsets

## Source Task

[https://github.com/simplesteph/kafka-connect-github-source/blob/master/src/main/java/com/simplesteph/kafka/GitHubSourceTask.java]