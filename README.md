runtime.properties ::
druid.service=druid/middleManager
druid.host=fcdevdruidhist
druid.enablePlaintextPort=false

# ===============================
# Worker capacity
# ===============================
druid.worker.capacity=4
druid.worker.baseTaskDirs=["var/druid/task"]

# ===============================
# Task JVM Options
# ===============================
druid.indexer.runner.javaOptsArray=["-server","-Xms4g","-Xmx4g","-XX:MaxDirectMemorySize=3g","-Duser.timezone=Asia/Kolkata","-Dfile.encoding=UTF-8","-XX:+ExitOnOutOfMemoryError","-Djava.util.logging.manager=org.apache.logging.log4j.jul.LogManager"]
# ===============================
# HTTP server threads
# ===============================
druid.server.http.numThreads=60

# ===============================
# Processing threads (Peons)
# ===============================
druid.indexer.fork.property.druid.processing.numMergeBuffers=4
druid.indexer.fork.property.druid.processing.buffer.sizeBytes=250MiB
druid.indexer.fork.property.druid.processing.numThreads=4

# ===============================
# Hadoop indexing temp path
# ===============================
druid.indexer.task.hadoopWorkingPath=var/druid/hadoop-tmp

# ===============================
# HTTPS
# ===============================
druid.enableTlsPort=true
druid.server.https.enable=true
druid.tlsPort=8191
druid.server.https.keyStorePath=/media/production-setup/apache-druid-34.0.0/ssl/middleManager-keystore.jks
druid.server.https.keyStorePassword=DruidPass123

# ===============================
# Shared Truststore
# ===============================
druid.server.https.trustStoreType=JKS
druid.server.https.trustStorePath=/media/production-setup/apache-druid-34.0.0/ssl/truststore.jks
druid.server.https.trustStorePassword=DruidPass123

common-runtime.properties ::
####################################################################
# Apache Druid - Common Runtime Properties
# SAFE for Historical & MiddleManager (HDFS ENABLED)
####################################################################

# ===============================
# Extensions
# ===============================
druid.extensions.directory=extensions
druid.extensions.loadList=["postgresql-metadata-storage","simple-client-sslcontext","druid-hdfs-storage","druid-parquet-extensions","druid-avro-extensions","druid-kafka-indexing-service","druid-deltalake-extensions"]
# ===============================
# Metadata Storage (PostgreSQL)
# ===============================
druid.metadata.storage.type=postgresql
druid.metadata.storage.connector.connectURI=jdbc:postgresql://fcdevdruidcordi:8283/fincore
druid.metadata.storage.connector.user=druid
druid.metadata.storage.connector.password=Password#1234

# ===============================
# ZooKeeper Configuration
# ===============================
druid.zk.service.host=jn1:2281,jn2:2281,jn3:2281
druid.zk.paths.base=/druid

# ===============================
# Deep Storage (HDFS REQUIRED)
# ===============================
druid.storage.type=hdfs
druid.storage.storageDirectory=hdfs://fincoredev:8022/druid/segments

# ===============================
# Service Discovery
# ===============================
druid.selectors.indexing.serviceName=druid/overlord
druid.selectors.coordinator.serviceName=druid/coordinator

# ===============================
# Coordinator / Overlord Properties (harmless here)
# ===============================
druid.coordinator.startDelay=PT30S
druid.coordinator.period=PT30S

druid.indexer.queue.startDelay=PT30S
druid.indexer.runner.type=remote
druid.indexer.storage.type=metadata

# ===============================
# Logging Configuration
# ===============================
druid.monitoring.monitors=["org.apache.druid.java.util.metrics.JvmMonitor"]
druid.emitter=logging
druid.emitter.logging.logLevel=info
druid.startup.logging.logProperties=true

# ===============================
# Time and Locale
# ===============================
user.timezone=Asia/Kolkata
user.language=en

####################################################################
# HTTPS / SSL Configuration
####################################################################
druid.enableTlsPort=true
druid.server.https.enable=true

druid.internal.http.useSSL=true
druid.client.https.enabled=true

druid.server.https.keyStoreType=JKS

druid.server.https.trustStoreType=JKS
druid.server.https.trustStorePath=/media/production-setup/apache-druid-34.0.0/ssl/truststore.jks
druid.server.https.trustStorePassword=DruidPass123

druid.client.https.trustStoreType=JKS
druid.client.https.trustStorePath=/media/production-setup/apache-druid-34.0.0/ssl/truststore.jks
druid.client.https.trustStorePassword=DruidPass123

jvm.config::
-server
-Xms8g
-Xmx8g
-XX:+ExitOnOutOfMemoryError
-Duser.timezone=Asia/Kolkata
-Dfile.encoding=UTF-8
-Djava.io.tmpdir=var/tmp
-Djava.util.logging.manager=org.apache.logging.log4j.jul.LogManager
