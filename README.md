runtime.properties ::
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


common-runtime.properties ::
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

jvm.config ::

-server
-Xms8g
-Xmx8g
-XX:+UseG1GC
-XX:MaxGCPauseMillis=100
-XX:+ExitOnOutOfMemoryError
-Duser.timezone=Asia/Kolkata
-Dfile.encoding=UTF-8
-Djava.io.tmpdir=var/tmp
-Djavax.net.ssl.trustStoreType=JKS
-Djavax.net.ssl.trustStore=/media/production-setup/apache-druid-34.0.0/ssl/truststore.jks
-Djavax.net.ssl.trustStorePassword=DruidPass123
-Djavax.net.ssl.keyStoreType=JKS
-Djavax.net.ssl.keyStore=/media/production-setup/apache-druid-34.0.0/ssl/druid-keystore.jks
-Djavax.net.ssl.keyStorePassword=DruidPass123
-Djava.util.logging.manager=org.apache.logging.log4j.jul.LogManager
--add-opens=jdk.management/com.sun.management.internal=ALL-UNNAMED

Console Error ::


Picked up JAVA_TOOL_OPTIONS: -Ddruid.node.type=middleManager
16:59:36.674 [main] ERROR org.apache.druid.cli.CliMiddleManager - Error when starting up.  Failing.
com.google.inject.ProvisionException: Unable to provision, see the following errors:

1) [Guice/ErrorInCustomProvider]: NullPointerException: must specify a trustStorePath
  while locating SSLContextProvider
  while locating SSLContext annotated with interface EscalatedGlobal
  at HttpClientModule.configure(HttpClientModule.java:79)
      \_ installed by: Modules$OverrideModule -> Modules$OverrideModule -> HttpClientModule
  at CoordinatorDiscoveryModule.getLeaderHttpClient(CoordinatorDiscoveryModule.java:51)
      \_ for 1st parameter
  at CoordinatorDiscoveryModule.getLeaderHttpClient(CoordinatorDiscoveryModule.java:51)
      \_ installed by: Modules$OverrideModule -> Modules$OverrideModule -> CoordinatorDiscoveryModule
  at CoordinatorPollingBasicAuthenticatorCacheManager.<init>(CoordinatorPollingBasicAuthenticatorCacheManager.java:88)
      \_ for 4th parameter
  at CoordinatorPollingBasicAuthenticatorCacheManager.class(CoordinatorPollingBasicAuthenticatorCacheManager.java:70)
  while locating CoordinatorPollingBasicAuthenticatorCacheManager
  at BasicSecurityDruidModule.createAuthenticatorCacheManager(BasicSecurityDruidModule.java:113)
      \_ installed by: Modules$OverrideModule -> BasicSecurityDruidModule
  while locating BasicAuthenticatorCacheManager

Learn more:
  https://github.com/google/guice/wiki/ERROR_IN_CUSTOM_PROVIDER

1 error

======================
Full classname legend:
======================
BasicAuthenticatorCacheManager:                   "org.apache.druid.security.basic.authentication.db.cache.BasicAuthenticatorCacheManager"
BasicSecurityDruidModule:                         "org.apache.druid.security.basic.BasicSecurityDruidModule"
CoordinatorDiscoveryModule:                       "org.apache.druid.guice.CoordinatorDiscoveryModule"
CoordinatorPollingBasicAuthenticatorCacheManager: "org.apache.druid.security.basic.authentication.db.cache.CoordinatorPollingBasicAuthenticatorCacheManager"
EscalatedGlobal:                                  "org.apache.druid.guice.annotations.EscalatedGlobal"
HttpClientModule:                                 "org.apache.druid.guice.http.HttpClientModule"
Modules$OverrideModule:                           "com.google.inject.util.Modules$OverrideModule"
SSLContext:                                       "javax.net.ssl.SSLContext"
SSLContextProvider:                               "org.apache.druid.https.SSLContextProvider"
========================
End of classname legend:
========================

        at com.google.inject.internal.InternalProvisionException.toProvisionException(InternalProvisionException.java:251) ~[guice-5.1.0.jar:?]
        at com.google.inject.internal.InjectorImpl$1.get(InjectorImpl.java:1104) ~[guice-5.1.0.jar:?]
        at com.google.inject.internal.InjectorImpl.getInstance(InjectorImpl.java:1134) ~[guice-5.1.0.jar:?]
        at org.apache.druid.guice.LifecycleModule$2.start(LifecycleModule.java:150) ~[druid-processing-34.0.0.jar:34.0.0]
        at org.apache.druid.cli.GuiceRunnable.initLifecycle(GuiceRunnable.java:137) [druid-services-34.0.0.jar:34.0.0]
        at org.apache.druid.cli.GuiceRunnable.initLifecycle(GuiceRunnable.java:94) [druid-services-34.0.0.jar:34.0.0]
        at org.apache.druid.cli.ServerRunnable.run(ServerRunnable.java:67) [druid-services-34.0.0.jar:34.0.0]
        at org.apache.druid.cli.Main.main(Main.java:112) [druid-services-34.0.0.jar:34.0.0]
Caused by: java.lang.NullPointerException: must specify a trustStorePath

