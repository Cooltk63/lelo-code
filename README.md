runtime.properties

####################################################################
# MiddleManager - runtime.properties (PRODUCTION)
# Based on working DEV config + Peon SSL fix for Basic Auth
####################################################################

druid.service=druid/middleManager
druid.host=fcproddruidhist1
druid.enablePlaintextPort=false

# ===============================
# Worker capacity
# ===============================
druid.worker.capacity=4
druid.worker.baseTaskDirs=["var/druid/task"]

# ===============================
# Task JVM Options
# ===============================
druid.indexer.runner.javaOptsArray=["-server","-Xms8g","-Xmx8g","-XX:MaxDirectMemorySize=3g","-Duser.timezone=Asia/Kolkata","-Dfile.encoding=UTF-8","-XX:+ExitOnOutOfMemoryError","-Djava.util.logging.manager=org.apache.logging.log4j.jul.LogManager"]

# ===============================
# HTTP server threads
# ===============================
druid.server.http.numThreads=60

# ===============================
# Processing threads (Peons)
# ===============================
druid.indexer.fork.property.druid.processing.numMergeBuffers=2
druid.indexer.fork.property.druid.processing.buffer.sizeBytes=536870912
druid.indexer.fork.property.druid.processing.numThreads=3

# ===============================
# Hadoop indexing temp path
# ===============================
druid.indexer.task.hadoopWorkingPath=var/druid/hadoop-tmp

# ===============================
# HTTPS - Server (this node identity)
# ===============================
druid.enableTlsPort=true
druid.server.https.enable=true
druid.tlsPort=8284
druid.server.https.keyStoreType=JKS
druid.server.https.keyStorePath=/media/production-setup/apache-druid-34.0.0/ssl/druid-keystore.jks
druid.server.https.keyStorePassword=DruidPass123
druid.server.https.certAlias=druid-cluster

# ===============================
# HTTPS - Truststore
# ===============================
druid.server.https.trustStoreType=JKS
druid.server.https.trustStorePath=/media/production-setup/apache-druid-34.0.0/ssl/truststore.jks
druid.server.https.trustStorePassword=DruidPass123

####################################################################
# CRITICAL: Peon Task SSL Configuration
# Production has Basic Auth (CoordinatorPollingBasicAuthenticatorCacheManager)
# which forces Druid to validate Peon SSL at startup.
# Without these fork.property lines MiddleManager crashes immediately.
# DEV works without these only because DEV has no Basic Auth module.
####################################################################

# Peon inherits HTTPS mode
druid.indexer.fork.property.druid.enableTlsPort=true
druid.indexer.fork.property.druid.internal.http.useSSL=true

# Peon client SSL (peon talks to Coordinator/Overlord over HTTPS)
druid.indexer.fork.property.druid.client.https.protocol=TLSv1.2
druid.indexer.fork.property.druid.client.https.trustStoreType=JKS
druid.indexer.fork.property.druid.client.https.trustStorePath=/media/production-setup/apache-druid-34.0.0/ssl/truststore.jks
druid.indexer.fork.property.druid.client.https.trustStorePassword=DruidPass123
druid.indexer.fork.property.druid.client.https.keyStoreType=JKS
druid.indexer.fork.property.druid.client.https.keyStorePath=/media/production-setup/apache-druid-34.0.0/ssl/druid-keystore.jks
druid.indexer.fork.property.druid.client.https.keyStorePassword=DruidPass123
druid.indexer.fork.property.druid.client.https.certAlias=druid-cluster
druid.indexer.fork.property.druid.client.https.validateHostnames=false

# Peon server SSL (peon itself serves HTTPS for task status callbacks)
druid.indexer.fork.property.druid.server.https.keyStoreType=JKS
druid.indexer.fork.property.druid.server.https.keyStorePath=/media/production-setup/apache-druid-34.0.0/ssl/druid-keystore.jks
druid.indexer.fork.property.druid.server.https.keyStorePassword=DruidPass123
druid.indexer.fork.property.druid.server.https.certAlias=druid-cluster
druid.indexer.fork.property.druid.server.https.trustStoreType=JKS
druid.indexer.fork.property.druid.server.https.trustStorePath=/media/production-setup/apache-druid-34.0.0/ssl/truststore.jks
druid.indexer.fork.property.druid.server.https.trustStorePassword=DruidPass123


common-runtime
####################################################################
# Apache Druid - Common Runtime Properties (PRODUCTION)
# Based on working DEV config - cleaned up invalid properties
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
druid.metadata.storage.connector.connectURI=jdbc:postgresql://fcprodhdfsjn7:5435/druid
druid.metadata.storage.connector.user=druid
druid.metadata.storage.connector.password=Password#1234

# ===============================
# ZooKeeper Configuration
# ===============================
druid.zk.service.host=jn1:2281,jn2:2281,jn3:2281,jn4:2281,jn5:2281,jn6:2281,jn7:2281
druid.zk.paths.base=/druid

# ===============================
# Deep Storage (HDFS)
# ===============================
druid.storage.type=hdfs
druid.storage.storageDirectory=hdfs://fincore/druid/segments

druid.indexer.logs.type=hdfs
druid.indexer.logs.directory=hdfs://fincore/druid/indexing-logs

# ===============================
# Service Discovery
# ===============================
druid.selectors.indexing.serviceName=druid/overlord
druid.selectors.coordinator.serviceName=druid/coordinator

# ===============================
# Coordinator / Overlord Properties
# ===============================
druid.coordinator.startDelay=PT30S
druid.coordinator.period=PT30S

druid.indexer.queue.startDelay=PT30S
druid.indexer.runner.type=remote
druid.indexer.storage.type=metadata

# ===============================
# Logging
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

# --- Enable HTTPS ---
druid.enableTlsPort=true
druid.server.https.enable=true

# --- Internal inter-node communication uses HTTPS ---
druid.internal.http.useSSL=true
# NOTE: druid.client.https.enabled is NOT a valid Druid property - removed

# --- Server keystore type (path set per node in runtime.properties) ---
druid.server.https.keyStoreType=JKS

# --- Common Truststore (same file on all nodes) ---
druid.server.https.trustStoreType=JKS
druid.server.https.trustStorePath=/media/production-setup/apache-druid-34.0.0/ssl/truststore.jks
druid.server.https.trustStorePassword=DruidPass123

# --- Client HTTPS (outbound calls from any node to other nodes) ---
# This is the section DEV has that makes client SSL work.
# Keep it simple like DEV - truststore only, no client keystore here.
druid.client.https.protocol=TLSv1.2
druid.client.https.trustStoreType=JKS
druid.client.https.trustStorePath=/media/production-setup/apache-druid-34.0.0/ssl/truststore.jks
druid.client.https.trustStorePassword=DruidPass123
druid.client.https.validateHostnames=false



jvm

-server
-Xms8g
-Xmx8g
-XX:+UseG1GC
-XX:MaxGCPauseMillis=100
-XX:+ExitOnOutOfMemoryError
-Duser.timezone=Asia/Kolkata
-Dfile.encoding=UTF-8
-Djava.io.tmpdir=var/tmp
-Djava.util.logging.manager=org.apache.logging.log4j.jul.LogManager
--add-opens=jdk.management/com.sun.management.internal=ALL-UNNAMED


this the out put I am getting and alsong with I have shared you the file 
[root@fcproddruidhist1 _common]# pwd
/media/production-setup/apache-druid-34.0.0/conf/druid/cluster/_common
[root@fcproddruidhist1 _common]#  -list -keystore /media/production-setup/apache-druid-34.0.0/ssl/druid-keystore.jks                                                                                                                         -storepass 'DruidPass123'
bash: -list: command not found...
[root@fcproddruidhist1 _common]# keytool -list -keystore /media/production-setup/apache-druid-34.0.0/ssl/druid-keysto                                                                                                                        re.jks -storepass 'DruidPass123'
Keystore type: JKS
Keystore provider: SUN

Your keystore contains 1 entry

druid-cluster, Mar 21, 2026, PrivateKeyEntry,
Certificate fingerprint (SHA-256): 6D:8B:75:D1:1F:8A:F8:D9:30:7E:3C:56:9D:D2:49:32:01:28:DF:0D:27:1D:E3:AA:FD:86:C8:9                                                                                                                        9:73:8E:A8:16

Warning:
The JKS keystore uses a proprietary format. It is recommended to migrate to PKCS12 which is an industry standard form                                                                                                                        at using "keytool -importkeystore -srckeystore /media/production-setup/apache-druid-34.0.0/ssl/druid-keystore.jks -de                                                                                                                        stkeystore /media/production-setup/apache-druid-34.0.0/ssl/druid-keystore.jks -deststoretype pkcs12".
[root@fcproddruidhist1 _common]# grep -i "basic-security\|basic_security" /media/production-setup/apache-druid-34.0.0                                                                                                                        /conf/druid/_common/common.runtime.properties
grep: /media/production-setup/apache-druid-34.0.0/conf/druid/_common/common.runtime.properties: No such file or direc                                                                                                                        tory
[root@fcproddruidhist1 _common]# grep -i "basic-security\|basic_security" /media/production-setup/apache-druid-34.0.0/conf/druid/_common/common.runtime.properties
grep: /media/production-setup/apache-druid-34.0.0/conf/druid/_common/common.runtime.properties: No such file or directory
[root@fcproddruidhist1 _common]# pwd
/media/production-setup/apache-druid-34.0.0/conf/druid/cluster/_common
