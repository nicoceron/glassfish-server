======================================================
Oracle Free Use Terms and Conditions (FUTC) License 
======================================================
https://www.oracle.com/downloads/licenses/oracle-free-license.html
===================================================================

ojdbc10-full.tar.gz - JDBC Thin Driver and Companion JARS
========================================================
This TAR archive (ojdbc10-full.tar.gz) contains the 19.26.0.0 release of the Oracle JDBC Thin driver(ojdbc10.jar), the Universal Connection Pool (ucp.jar) and other companion JARs grouped by category. 

(1) ojdbc10.jar (4569302 bytes) - 
(SHA1 Checksum: c32d35180dca7787286d0544d7bfdd05f4b6be06)
Oracle JDBC Driver compatible with JDK8, JDK9, and JDK11;
(2) ucp.jar (1701064 bytes) - (SHA1 Checksum: dfcf6a1bbc8c0171ec2f588ceba4f9026d3f9354)
Universal Connection Pool classes for use with JDK8, JDK9, and JDK11 -- for performance, scalability, high availability, sharded and multitenant databases.
(3) ojdbc.policy (12134 bytes) - Sample security policy file for Oracle Database JDBC drivers

======================
Security Related JARs
======================
Java applications require some additional jars to use Oracle Wallets. 
You need to use all the three jars while using Oracle Wallets. 

(4) oraclepki.jar (310576 bytes ) - (SHA1 Checksum: c449555be31cf712a231e7b5df42ace9ebc3b30a
Additional jar required to access Oracle Wallets from Java
(5) osdt_cert.jar (210640 bytes) - (SHA1 Checksum: fd3031bdba8c705408ed43101864cbae308d664c)
Additional jar required to access Oracle Wallets from Java
(6) osdt_core.jar (312593 bytes) - (SHA1 Checksum: 38c8c64b843b0408109a01d31b8b488787b7d256)
Additional jar required to access Oracle Wallets from Java

=============================
JARs for NLS and XDK support 
=============================
(7) orai18n.jar (1664184 bytes) - (SHA1 Checksum: 65b0280f7e5a94ecf9557265f71642bcdeaac96f) 
Classes for NLS support
(8) xdb.jar (129474 bytes) - (SHA1 Checksum: c17bd41b29965f552367ba0403bb3d4f06b433df)
Classes to support standard JDBC 4.x java.sql.SQLXML interface 
(9) xmlparserv2.jar (1935805 bytes) - (SHA1 Checksum: a19ad1686dc12193ff4fb4fa1655f86d16740338)
Classes to support standard JDBC 4.x java.sql.SQLXML interface 
(10) xmlparserv2_sans_jaxp_services.jar (1933272 bytes) - (SHA1 Checksum: 58bcb60b44f0248bc707d0c09364253155445e89) 
Classes to support standard JDBC 4.x java.sql.SQLXML interface

====================================================
JARs for Real Application Clusters(RAC), ADG, or DG 
====================================================
(11) ons.jar (156646 bytes ) - (SHA1 Checksum: 3554dcf30d2ab4facdf73f455fae6f2acebde90a)
for use by the pure Java client-side Oracle Notification Services (ONS) daemon
(12) simplefan.jar (32397 bytes) - (SHA1 Checksum: 7401f50eb2ade98a4195106a541c60c8a28b8db2)
Java APIs for subscribing to RAC events via ONS; simplefan policy and javadoc

==================================================================================
NOTE: The diagnosability JARs **SHOULD NOT** be used in the production environment. 
These JARs (ojdbc10_g.jar,ojdbc10dms.jar, ojdbc10dms_g.jar) are meant to be used in the 
development, testing, or pre-production environment to diagnose any JDBC related issues. 

=====================================
OJDBC - Diagnosability Related JARs
===================================== 

(13) ojdbc10_g.jar (7645368 bytes) - (SHA1 Checksum: 150e7cd128886b3bfa47c7207ea518ba7c5c28db)
Same as ojdbc10.jar except compiled with "javac -g" and contains tracing code.

(14) ojdbc10dms.jar (6355130 bytes) - (SHA1 Checksum: f2e3b8db63f969109ec78dd3d4bed85410157607)
Same as ojdbc10.jar, except that it contains instrumentation to support DMS and limited java.util.logging calls.

(15) ojdbc10dms_g.jar (7675005 bytes) - (SHA1 Checksum: b1a9e56fadcdf1c3c610f79f4264e2bc850a9a34)
Same as ojdbc10_g.jar except that it contains instrumentation to support DMS.

(16) dms.jar (2194533 bytes) - (SHA1 Checksum: 630235f20bb2ffd6c0d9c8fa09d07554566f05b0)
dms.jar required for DMS-enabled JAR files.

==================================================================
Oracle JDBC and UCP - Javadoc and README
==================================================================

(17) JDBC-Javadoc-19c.jar (2312789 bytes) - JDBC API Reference 19c

(18) ucp-Javadoc-19c.jar (366701 bytes) - UCP Java API Reference 19c

(19) simplefan-Javadoc-19c.jar (84142 bytes) - Simplefan API Reference 19c 

(20) xdb-Javadoc-19c.jar (2861664 bytes) - XDB API Reference 19c 

(21) xmlparserv2-Javadoc-19c.jar (2861664 bytes) - xmlparserv2 API Reference 19c 

(22) Jdbc-Readme.txt: It contains general information about the JDBC driver and bugs that have been fixed in the 19.26.0.0 release. 

(23) UCP-Readme.txt: It contains general information about UCP and bugs that are fixed in the 19.26.0.0 release. 


=================
USAGE GUIDELINES
=================
Refer to the JDBC Developers Guide (https://docs.oracle.com/en/database/oracle/oracle-database/19/jjdbc/index.html) and Universal Connection Pool Developers Guide (https://docs.oracle.com/en/database/oracle/oracle-database/19/jjucp/index.html) for more details.
