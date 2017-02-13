Apache HTTP Server changeLog for 2.4/2.5

                                                         -*- coding: utf-8 -*-

Changes with Apache 2.4.25

  *) Fix some build issues related to various modules.
     [Rainer Jung]

Changes with Apache 2.4.24

  *) SECURITY: CVE-2016-8740 (cve.mitre.org)
     mod_http2: Mitigate DoS memory exhaustion via endless
     CONTINUATION frames.
     [Naveen Tiwari <naveen.tiwari@asu.edu> and CDF/SEFCOM at Arizona State
     University, Stefan Eissing]

  *) SECURITY: CVE-2016-5387 (cve.mitre.org)
     core: Mitigate [f]cgi "httpoxy" issues.
     [Dominic Scheirlinck <dominic vendhq.com>, Yann Ylavic]

  *) SECURITY: CVE-2016-2161 (cve.mitre.org)
     mod_auth_digest: Prevent segfaults during client entry allocation when
     the shared memory space is exhausted.
     [Maksim Malyutin <m.malyutin dsec.ru>, Eric Covener, Jacob Champion]

  *) SECURITY: CVE-2016-0736 (cve.mitre.org)
     mod_session_crypto: Authenticate the session data/cookie with a
     MAC (SipHash) to prevent deciphering or tampering with a padding
     oracle attack.  [Yann Ylavic, Colm MacCarthaigh]

  *) SECURITY: CVE-2016-8743 (cve.mitre.org)
     Enforce HTTP request grammar corresponding to RFC7230 for request lines
     and request headers, to prevent response splitting and cache pollution by
     malicious clients or downstream proxies. [William Rowe, Stefan Fritsch]

  *) Validate HTTP response header grammar defined by RFC7230, resulting
     in a 500 error in the event that invalid response header contents are
     detected when serving the response, to avoid response splitting and cache
     pollution by malicious clients, upstream servers or faulty modules.
     [Stefan Fritsch, Eric Covener, Yann Ylavic]

  *) mod_rewrite: Limit runaway memory use by short circuiting some kinds of
     looping RewriteRules when the local path significantly exceeds 
     LimitRequestLine.  PR 60478. [Jeff Wheelhouse <apache wheelhouse.org>]

  *) mod_ratelimit: Allow for initial "burst" amount at full speed before
     throttling: PR 60145 [Andy Valencia <ajv-etradanalhos vsta.org>,
     Jim Jagielski]

  *) mod_socache_memcache: Provide memcache stats to mod_status.
     [Jim Jagielski]

  *) http_filters: Fix potential looping in new check_headers() due to new
     pattern of ap_die() from http header filter. Explicitly clear the
     previous headers and body.

  *) core: Drop Content-Length header and message-body from HTTP 204 responses.
     PR 51350 [Luca Toscano]

  *) mod_proxy: Honor a server scoped ProxyPass exception when ProxyPass is
     configured in <Location>, like in 2.2. PR 60458.
     [Eric Covener]

  *) mod_lua: Fix default value of LuaInherit directive. It should be 
     'parent-first' instead of 'none', as per documentation.  PR 60419
     [Christophe Jaillet]

  *) core: New directive HttpProtocolOptions to control httpd enforcement
     of various RFC7230 requirements. [Stefan Fritsch, William Rowe]

  *) core: Permit unencoded ';' characters to appear in proxy requests and
     Location: response headers. Corresponds to modern browser behavior.
     [William Rowe]

  *) core: ap_rgetline_core now pulls from r->proto_input_filters.

  *) core: Correctly parse an IPv6 literal host specification in an absolute
     URL in the request line. [Stefan Fritsch]

  *) core: New directive RegisterHttpMethod for registering non-standard
     HTTP methods. [Stefan Fritsch]

  *) mod_socache_memcache: Pass expiration time through to memcached.
     [Faidon Liambotis <paravoid debian.org>, Joe Orton]

  *) mod_cache: Use the actual URI path and query-string for identifying the
     cached entity (key), such that rewrites are taken into account when
     running afterwards (CacheQuickHandler off).  PR 21935.  [Yann Ylavic]

  *) mod_http2: new directive 'H2EarlyHints' to enable sending of HTTP status
     103 interim responses. Disabled by default. [Stefan Eissing]
     
  *) mod_ssl: Fix quick renegotiation (OptRenegotiaton) with no intermediate
     in the client certificate chain.  PR 55786.  [Yann Ylavic]

  *) event: Allow to use the whole allocated scoreboard (up to ServerLimit
     slots) to avoid scoreboard full errors when some processes are finishing
     gracefully. Also, make gracefully finishing processes close all
     keep-alive connections. PR 53555. [Stefan Fritsch]

  *) mpm_event: Don't take over scoreboard slots from gracefully finishing
     threads. [Stefan Fritsch]

  *) mpm_event: Free memory earlier when shutting down processes.
     [Stefan Fritsch]

  *) mod_status: Display the process slot number in the async connection
     overview. [Stefan Fritsch]

  *) mod_dir: Responses that go through "FallbackResource" might appear to
     hang due to unterminated chunked encoding. PR58292. [Eric Covener]

  *) mod_dav: Fix a potential cause of unbounded memory usage or incorrect
     behavior in a routine that sends <DAV:response>'s to the output filters.
     [Evgeny Kotkov]

  *) mod_http2: new directive 'H2PushResource' to enable early pushes before 
     processing of the main request starts. Resources are announced to the 
     client in Link headers on a 103 early hint response. 
     All responses with status code <400 are inspected for Link header and
     trigger pushes accordingly. 304 still does prevent pushes.
     'H2PushResource' can mark resources as 'critical' which gives them higher
     priority than the main resource. This leads to preferred scheduling for
     processing and, when content is available, will send it first. 'critical'
     is also recognized on Link headers. [Stefan Eissing]
     
  *) mod_proxy_http2: uris in Link headers are now mapped back to a suitable
     local url when available. Relative uris with an absolute path are mapped
     as well. This makes reverse proxy mapping available for resources
     announced in this header. 
     With 103 interim responses being forwarded to the main client connection,
     this effectively allows early pushing of resources by a reverse proxied
     backend server. [Stefan Eissing]
     
  *) mod_proxy_http2: adding support for newly proposed 103 status code.
     [Stefan Eissing]
     
  *) mpm_unix: Apache fails to start if previously crashed then restarted with
     the same PID (e.g. in container).  PR 60261.
     [Val <valentin.bremond gmail.com>, Yann Ylavic]

  *) mod_http2: unannounced and multiple interim responses (status code < 200)
     are parsed and forwarded to client until a final response arrives.
     [Stefan Eissing]
  
  *) mod_proxy_http2: improved robustness when main connection is closed early
     by resetting all ongoing streams against the backend.
     [Stefan Eissing]
  
  *) mod_http2: allocators from slave connections are released earlier,
     resulting in less overall memory use on busy, long lived connections.
     [Stefan Eissing]
     
  *) mod_remoteip: Pick up where we left off during a subrequest rather
     than running with the modified XFF but original TCP address.
     PR 49839/PR 60251

  *) http: Respond with "408 Request Timeout" when a timeout occurs while
     reading the request body.  [Yann Ylavic]

  *) mod_http2: connection shutdown revisited: corrected edge cases on
     shutting down ongoing streams, changed log warnings to be less noisy
     when waiting on long running tasks. [Stefan Eissing]

  *) mod_http2: changed all AP_DEBUG_ASSERT to ap_assert to have them 
     available also in normal deployments. [Stefan Eissing]

  *) mod_http2/mod_proxy_http2: 100-continue handling now properly implemented
     up to the backend. Reused HTTP/2 proxy connections with more than a second
     not used will block request bodies until a PING answer is received.
     Requests headers are not delayed by this, since they are repeatable in
     case of failure. This greatly increases robustness, especially with
     busy server and/or low keepalive connections. [Stefan Eissing]
     
  *) mod_proxy_http2: fixed duplicate symbols with mod_http2.
     [Stefan Eissing]
  
  *) mod_http2: rewrite of how responses and trailers are transferred between
     master and slave connection. Reduction of internal states for tasks
     and streams, stability. Heuristic id generation for slave connections
     to better keep promise of connection ids unique at given point int time.
     Fix for mod_cgid interop in high load situtations. 
     Fix for handling of incoming trailers when no request body is sent.
     [Stefan Eissing]
  
  *) mod_http2: fix suspended handling for streams. Output could become
     blocked in rare cases. [Stefan Eissing]

  *) mpm_winnt: Prevent a denial of service when the 'data' AcceptFilter is in
     use by replacing it with the 'connect' filter. PR 59970. [Jacob Champion]

  *) mod_cgid: Resolve a case where a short CGI response causes a subsequent
     CGI to be killed prematurely, resulting in a truncated subsequent
     response. [Eric Covener]

  *) mod_proxy_hcheck: Set health check URI and expression correctly for health
     check worker. PR 60038 [zdeno <zdeno@scnet.sk>]

  *) mod_http2: if configured with nghttp2 1.14.0 and onward, invalid request
     headers will immediately reset the stream with a PROTOCOL error. Feature
     logged by module on startup as 'INVHD' in info message.
     [Stefan Eissing]
     
  *) mod_http2: fixed handling of stream buffers during shutdown.
     [Stefan Eissing]
     
  *) mod_reqtimeout: Fix body timeout disabling for CONNECT requests to avoid
     triggering mod_proxy_connect's AH01018 once the tunnel is established.
     [Yann Ylavic]

  *) ab: Set the Server Name Indication (SNI) extension on outgoing TLS
     connections (unless -I is specified), according to the Host header (if
     any) or the requested URL's hostname otherwise.  [Yann Ylavic]

  *) mod_proxy_fcgi: avoid loops when ProxyErrorOverride is enabled
     and the error documents are proxied. PR 55415. [Luca Toscano]

  *) mod_proxy_fcgi: read the whole FCGI response even when the content
     has not been modified (HTTP 304) or in case of a precondition failure
     (HTTP 412) to avoid subsequent bogus reads and confusing
     error messages logged. [Luca Toscano]

  *) mod_http2: h2 status resource follows latest draft, see
     http://www.ietf.org/id/draft-benfield-http2-debug-state-01.txt
     [Stefan Eissing]
     
  *) mod_http2: handling graceful shutdown gracefully, e.g. handling existing
     streams to the end. [Stefan Eissing]
  
  *) mod_proxy_{http,ajp,fcgi}: don't reuse backend connections with data
     available before the request is sent.  PR 57832.  [Yann Ylavic]

  *) mod_proxy_balancer: Prevent redirect loops between workers within a
     balancer by limiting the number of redirects to the number balancer
     members. PR 59864 [Ruediger Pluem]

  *) mod_proxy: Correctly consider error response codes by the backend when
     processing failonstatus. PR 59869 [Ruediger Pluem]

  *) mod_dav: Add dav_get_provider_name() function to obtain the name
     of the provider from mod_dav.  [Graham Leggett]

  *) mod_dav: Add support for childtags to dav_error.
     [Jari Urpalainen <jari.urpalainen nokia.com>]

  *) mod_proxy_fcgi: Fix 2.4.23 breakage for mod_rewrite per-dir and query 
     string showing up in SCRIPT_FILENAME. PR59815

  *) mod_include: Fix a potential memory misuse while evaluating expressions.
     PR59844. [Eric Covener]

  *) mod_http2: new H2CopyFiles directive that changes treatment of file
     handles in responses. Necessary in order to fix broken lifetime handling
     in modules such as mod_wsgi.
  
  *) mod_http2: removing timeouts on master connection while requests are
     being processed. Requests may timeout, but the master only times out when
     no more requests are active. [Stefan Eissing]
     
  *) mod_http2: fixes connection flush when answering SETTINGS without any
     stream open. [Moto Ishizawa <@summerwind>, Stefan Eissing]
     


  [Apache 2.3.0-dev includes those bug fixes and changes with the
   Apache 2.2.xx tree as documented, and except as noted, below.]

Changes with Apache 2.2.x and later:

  *) http://svn.apache.org/viewvc/httpd/httpd/branches/2.2.x/CHANGES?view=markup

Changes with Apache 2.0.x and later:

  *) http://svn.apache.org/viewvc/httpd/httpd/branches/2.0.x/CHANGES?view=markup
----------------------------------------------------------------------------------------------------------------------------------------------------

Tomcat 7.0.75 (violetagg)
Cluster
add	Make the accessTimeout configurable in BackupManager. The accessTimeout is used as a timeout period for PING in replication map. (kfujino)
Web applications
fix	Ensure the ASF logo image is correctly displayed in docs and host-manager applications. (violetagg)

---------------------------------------------------------------------------------------------------------------------------------------------------
Eclipse Project Release Notes

Release 4.6.0 (Neon)

Last revised May 30, 2016

This software is OSI Certified Open Source Software.
OSI Certified is a certification mark of the Open Source Initiative.

Eclipse Project Release Notes
Target Operating Environments
Compatibility with Previous Releases
Compatibility of Release 4.6 with 4.5
Known Issues
General problems
General - Startup
Installation/Configuration issues that can cause Eclipse to fail start
Invalid characters in install directory prevents Eclipse from starting
Hanging during class loading when out of permanent generation memory
General - GCJ
Problems with classloaders in created threads
Deadlock creating executable extension in Plugin.startup
Potential Problems Converting Plug-in Manifests
Location for Debug Options File on Mac OS
Issues with JNI that use FindClass
Platform - Ant
Custom Ant tasks and Ant types must be separate from plug-in library JARs
XDoclet support from within Eclipse
Ant Editor code completion based on Ant 1.6.x
Setting build loggers not supported when debugging Ant builds
Renaming an External Tool builder set to run during auto-build will cause errors
Slow typing/saving of the Ant editor with imports that define numerous macrodefs
Ant 1.8.x reports missing libraries as build failures
Platform - User Assistance
Welcome page not displayed properly (Linux/Unix)
Help browser tool bar buttons do not work for some documents
Help documents not displayed in a browser or very slow document loading (Windows only)
Working disconnected from the network (Windows only)
Using Internet Explorer in offline mode (Windows only)
Platform - UI
Dirty state not tracked properly for OLE documents (Windows only)
OLE document crashes can cause Eclipse to also crash (Windows only)
Toolbars only containing contributed controls exhibit display errors on Mac/Linux
Allocating enough memory and solving OutOfMemoryError
Capabilities and Activities don't affect the menus and toolbars
Platform - SWT
Non-uniform scaling of text vs. icons when using intermediate scaling factors on high-DPI displays
Eclipse plug-in based on the SWT Browser throws exception
SWT Browser widget does not work on some recent Linux versions e.g Ubuntu 16.04
SWT Browser widget crashes when run with XULRunner 31 run-time (Linux only)
IME conversion problem (Solaris GTK only)
Eclipse won't start (Linux GTK PPC only)
Eclipse icon is duplicated in task-bar on Windows
BIDI Segments in Text controls
Block Selection functionality provided by StyledText is not BIDI aware
Platform - Team - CVS
CVS server compatibility
Connection cannot be found after initially missing
Received broken pipe signal" error from server
Terminated with fatal signal 10" error from server
error using ext connection method
A disabled CVS capability may not be auto-enabled in existing workspaces
Builder output files may appear as changed
Enabling GNOME proxy support
Platform - Install/Update
Manually installing features and plug-ins on a FAT file system (Windows only)
Connecting to untrusted sites using https
Extension location is lost if the install path changes
Java development tools (JDT)
Multiple regions formatting in a given source snippet
Searching for constant field references
Cut, copy, paste not working for linked resources in views showing Java elements
Java working sets not working correctly for elements from JRE system library container
Breakpoints unreliable running Sun 1.6.0_14
Suspend on uncaught exception overrides exception breakpoint location filters
Running Java programs with non-Latin-1 characters in package or class names
Cannot run or debug class in a project with GB18030 characters in project name
Cannot detect installed JRE with GB18030 characters in path name
Cannot generate Javadoc for packages with GB18030 characters in the name
Unable to debug stack overflows
Evaluation limitation
Missing debug attributes
Using Hot Code Replace
Scrapbook
Debugging over slow connections
Updating of inspected values
Stepping over native methods that perform I/O
VM and process termination running on IBM 1.3 JVM on Linux (Linux only)
Java Annotation Processing
Java indexing encounters problems when a folder is used both as a source and a class folder
Plug-in Development Environment (PDE)
Feature manifest editor does not preserve all comments
PDE will not unzip source zips of some plug-ins
Emacs key bindings do not work in manifest editor fields
Export of plug-in may silently drop classes
Compilation errors when exporting projects not stored outside of the workspace
Headless build needs to be run from a fully qualified path
Importing in Eclipse application fails if plug-in exists in host workspace
Reusing a workspace after changing architectures silently breaks PDE models
Missing @since tag API Tools problems on interface fields containing @noreference tag
Running Eclipse
Allocating enough memory and solving OutOfMemoryErrors
Selecting a workspace
Specifying the Java virtual machine
Mac OS X
Shared Install
Special Operating System Requirements
AIX
HPUX
Solaris
Upgrading Workspace from a Previous Release
Users who don't use "-data"
Users who do use "-data"
Users who use User Libraries or classpath containers that contain JARs referencing other libraries via Class-Path in the MANIFEST.MF
Dropped in bundles may not resolve after upgrade
Interoperability with Previous Releases
Interoperability of Release 4.6 with previous releases
Sharing projects between heterogeneous Eclipse 4.6 and 4.5
Using Eclipse 4.6 to develop plug-ins that work in Eclipse 4.5
Appendix: Execution Environment by Bundle
Target Operating Environments
In order to remain current, each Eclipse Project release targets reasonably current operating environments.

Most of the Eclipse SDK is "pure" Java code and has no direct dependence on the underlying operating system. The chief dependence is therefore on the Java Platform itself. Portions are targeted to specific classes of operating environments, requiring their source code to only reference facilities available in particular class libraries (e.g. J2ME Foundation 1.1, J2SE 1.4, Java 5, etc).

In general, the 4.6 release of the Eclipse Project is developed on Java SE 8 VMs. As such, the Eclipse SDK as a whole is targeted at all modern, desktop Java VMs.

Appendix 1 contains a table that indicates the class library level required for each bundle.

There are many different implementations of the Java Platform running atop a variety of operating systems. We focus our testing on a handful of popular combinations of operating system and Java Platform; these are our reference platforms. Eclipse undoubtedly runs fine in many operating environments beyond the reference platforms we test. However, since we do not systematically test them we cannot vouch for them. Problems encountered when running Eclipse on a non-reference platform that cannot be recreated on any reference platform will be given lower priority than problems with running Eclipse on a reference platform.

Eclipse 4.6 is tested and validated on a number of reference platforms. For the complete list, see Target Environments in the 4.6 Plan.

As stated above, we expect that Eclipse works fine on other current Java VM and OS versions but we cannot flag these as reference platforms without significant community support for testing them.

The Eclipse SDK is designed as the basis for internationalized products. The user interface elements provided by the Eclipse SDK components, including dialogs and error messages, are externalized. The English strings are provided as the default resource bundles.

Latin-1, DBCS, and BiDi locales are supported by the Eclipse SDK on all reference platforms.

The Eclipse SDK supports GB 18030 (level 1), the Chinese code page standard, on Windows, Linux and the Macintosh.

German and Japanese locales are tested.

Compatibility with Previous Releases
Compatibility of Release 4.6 with 4.5
Eclipse 4.6 is compatible with Eclipse 4.5 (and all earlier 3.x versions).

API Contract Compatibility: Eclipse SDK 4.6 is upwards contract-compatible with Eclipse SDK 4.5 except in those areas noted in the Eclipse 4.6 Plug-in Migration Guide. Programs that use affected APIs and extension points will need to be ported to Eclipse SDK 4.6 APIs. Downward contract compatibility is not supported. There is no guarantee that compliance with Eclipse SDK 4.6 APIs would ensure compliance with Eclipse SDK 4.5 APIs. Refer to Evolving Java-based APIs for a discussion of the kinds of API changes that maintain contract compatibility.

Binary (plug-in) Compatibility: Eclipse SDK 4.6 is upwards binary-compatible with Eclipse SDK 4.5 except in those areas noted in the Eclipse 4.6 Plug-in Migration Guide . Downward plug-in compatibility is not supported. Plug-ins for Eclipse SDK 4.6 will not be usable in Eclipse SDK 4.5. Refer to Evolving Java-based APIs for a discussion of the kinds of API changes that maintain binary compatibility.

Source Compatibility: Eclipse SDK 4.6 is upwards source-compatible with Eclipse SDK 4.5 except in the areas noted in the Eclipse 4.6 Plug-in Migration Guide . This means that source files written to use Eclipse SDK 4.5 APIs might successfully compile and run against Eclipse SDK 4.6 APIs, although this is not guaranteed. Downward source compatibility is not supported. If source files use new Eclipse SDK APIs, they will not be usable with an earlier version of the Eclipse SDK.

Workspace Compatibility: Eclipse SDK 4.6 is upwards workspace-compatible with earlier 3.x and 4.x versions of the Eclipse SDK unless noted. This means that workspaces and projects created with Eclipse SDK 4.5, 4.4, 4.3, 4.2, ... 3.0 can be successfully opened by Eclipse SDK 4.6 and upgraded to a 4.6 workspace. This includes both hidden metadata, which is localized to a particular workspace, as well as metadata files found within a workspace project (e.g., the .project file), which may propagate between workspaces via file copying or team repositories. Individual plug-ins developed for Eclipse SDK 4.6 should provide similar upwards compatibility for their hidden and visible workspace metadata created by earlier versions; 4.6 plug-in developers are responsible for ensuring that their plug-ins recognize metadata from earlier versions and process it appropriately. User interface session state may be discarded when a workspace is upgraded. Downward workspace compatibility is not supported. A workspace created (or opened) by a product based on Eclipse 4.6 will be unusable with a product based on an earlier version of Eclipse. Visible metadata files created (or overwritten) by Eclipse 4.6 will generally be unusable with earlier versions of Eclipse.

Non-compliant usage of API's: All non-API methods and classes, and certainly everything in a package with "internal" in its name or x-internal in the bundle manifest entry, are considered implementation details which may vary between operating environment and are subject to change without notice. Client plug-ins that directly depend on anything other than what is specified in the Eclipse SDK API are inherently unsupportable and receive no guarantees about compatibility within a single release much less with earlier releases. Refer to How to Use the Eclipse API for information about how to write compliant plug-ins.

Known Issues
Note: Bug numbers refer to the Eclipse project bug database at http://bugs.eclipse.org/bugs/

General problems
General - Startup
Installation/Configuration issues that can cause Eclipse to fail start

Here are some common problems that can cause Eclipse not to start:

As shown above, Eclipse 4.6 requires at least a Java SE 8. Perhaps an older version of the VM is being found in your path. To explicitly specify which VM to run with, use the Eclipse -vm command-line argument. (See also the Running Eclipse section below.)
Running Eclipse on Gentoo Linux may result in the following error message:
* run-java-tool is not available for sun-jdk-1.6 on i686
* IMPORTANT: some Java tools are not available on some VMs on some architectures
If this occurs, start Eclipse by specifying a -vm argument, either specify the path to a java vm or use: eclipse -vm `java-config --java` (bug 176021)
Eclipse must be installed to a clean directory and not installed over top of a previous installation. If you have done this then please re-install to a new directory. If your workspace is in a child directory of your old installation directory, then see the instructions below on "Upgrading Workspace from a Previous Release".
Java sometimes has difficulty detecting whether a file system is writable. In particular, the method java.io.File.canWrite() appears to return true in unexpected cases (e.g., using Windows drive sharing where the share is a read-only Samba drive). The Eclipse runtime generally needs a writable configuration area and as a result of this problem, may erroneously detect the current configuration location as writable. The net result is that Eclipse will fail to start and depending on the circumstances, may fail to write a log file with any details. To work around this, we suggest users experiencing this problem set their configuration area explicitly using the -configuration command line argument. (bug 67719)
Invalid characters in install directory prevents Eclipse from starting

Eclipse will fail to launch if installed in a directory whose path contains certain invalid characters, including :%#<>"!. The workaround is to install Eclipse in a directory whose path does not contain invalid characters. (bugs 3109 and 17281)

Hanging during class loading when out of permanent generation memory

The Oracle JVM may hang indefinitely during class loading if it runs out of permanent generation memory. This will cause CPU usage to stay at 100% until the process is ended. See the section Running Eclipse for details on addressing this VM problem.

General - GCJ
GCJ is an effort by the GCC team to provide an open source Java compiler and runtime environment to interpret Java bytecode. Unfortunately, the GCJ runtime environment is not an environment that is often tested on by Eclipse developers.

The most common problems surrounding GCJ are:

Eclipse does not start at all
Eclipse throws a 'java.lang.ClassNotFoundException: org.eclipse.core.runtime.Plugin' that can be found in the logs (located in workspace/.metadata/.log)
The workspace's log file is a good place to check to identify whether GCJ is being used or not. Every Eclipse log session is prepended with information about the runtime environment that was used to run Eclipse. The log may include something like the following: java.fullversion=GNU libgcj 4.2.1 (Debian 4.2.1-5)

If Eclipse does start, one can check which runtime environment is being used to run Eclipse by going to Help > About Eclipse SDK > Installation Details > Configuration. The About dialog itself can also provide other information, the build identifier can be of particular interest as it is tagged by some distributions. This allows the user to identify whether Eclipse was downloaded through the distribution's package management system or directly from the eclipse.org web site. 
Such as:
Build id: M20070212-1330 (Ubuntu version: 3.2.2-0ubuntu3)

The two most common workarounds are:

download the Eclipse binary from eclipse.org directly
run Eclipse using an alternate Java runtime environment
To download Eclipse, try one of the links below:

http://www.eclipse.org/downloads/
http://download.eclipse.org/eclipse/downloads/
It is imperative that 64-bit builds are downloaded and used if a 64-bit Java runtime environment has been installed. Below are two sample tarball names of version 4.6 of the Eclipse SDK packaged for 32-bit and 64-bit processors.
eclipse-SDK-4.6-linux-gtk.tar.gz (32-bit) 
eclipse-SDK-4.6-linux-gtk-x86_64.tar.gz (64-bit)

To run Eclipse with an alternate Java runtime environment, the path to the Java virtual machine's binary must be identified. With an Eclipse installation from the distribution, altering the $PATH variable to include the path to the alternate Java runtime environment is often not enough as the Eclipse that Linux distributions package often performs a scan internally to pick up GCJ by itself whilst ignoring what's on the $PATH. An example of the terminal's output is shown below:
searching for compatible vm...
testing /usr/lib/jvm/java-gcj...found

Once the path to the virtual machine's binary has been identified, try running Eclipse with the following command:
./eclipse -vm /path/to/jre/bin/java

For an actual example, it might look something like the following:
./eclipse -vm /usr/lib/jvm/sun-java-6/bin/java
./eclipse -vm /opt/sun-jdk-1.6.0.02/bin/java

If this seems to solve the problem, it is likely that the problem really was related to the use of GCJ as the Java runtime for running Eclipse. The eclipse.ini file located within Eclipse's folder can be altered to automatically pass this argument to Eclipse at startup. An example of its content is presented below:
-showsplash
org.eclipse.platform
-vm
/opt/sun-jdk-1.6.0.02/bin/java
-vmargs
-Xms40m
-Xmx512m

Note that every argument must be on its own line. More information about the eclipse.ini file can be found at http://wiki.eclipse.org/Eclipse.ini.

If problems persists after downloading an installation of Eclipse from eclipse.org and using a supported Java runtime environment (a list of which may be found above), you can seek further assistance through the forums, the IRC channel, and/or bugzilla.

Problems with classloaders in created threads

There is a known issue with trying to load classes from a newly-created thread using a class loader different from the plug-in class loader. The result will be a ClassNotFoundException . As a workaround, do the following:

Create a thread in which to run your code.
Send yourThread.setContextClassLoader(yourClassLoader); // you can find your classloader by grabbing a class it loaded (YourPluginClass.class.getClassLoader())
Run your code in the newly created thread.
If you set the context class loader for the current thread, you are competing with other users of the thread (all of Eclipse), so the results will be unpredictable. However, there should be no problem in practice provided you reset the context class loader back to its original value when your use in the current thread is complete. (bug 8907)

Deadlock creating executable extension in Plugin.startup

If Plugin.startup code is too complex and performs tasks such as creating an executable extension, a deadlock situation can be created. Only simple bookkeeping tasks should be performed in Plugin.startup code. (bug 5875)

Potential Problems Converting Plug-in Manifests

If your plug-in ships with a plug-in manifest and not an OSGi bundle manifest, is shipped as a JAR file, and contains a nested JAR file then there may be problems in the automatic generation of the bundle manifest file. The packages defined in the nested JAR may not be exported correctly in the Export-packages bundle manifest header. To work around this you should ship your plug-in with a bundle manifest. (bug 97689)

Location for Debug Options File on Mac OS

If you are running in debug mode on Mac OS, the default location for the .options file is inside the application bundle in the Eclipse.app/Contents/MacOS directory (like the eclipse.ini). (bug 88782)

Issues with JNI that use FindClass

There may be issues when using a JNI implementation that uses FindClass in a function where the JNIEnv pointer is not available, such as in a C callback (bug 125250). The reason is that FindClass, in this case, uses the application class loader to find the class. If the desired class is in the classpath used for the application classloader (e.g. defined by the VM argument -cp <classpath>), as it would typically be in a stand-alone application, there is no problem. However, under Eclipse, the application classloader does not have access to classes contained in plug-ins. Eclipse uses its own class loader to find classes contained in plug-ins.

The proper plug-in class loader is used by FindClass in JNI functions which are passed the JNIEnv pointer, but not when you have to use AttachCurrentThread to get the JNIEnv pointer. In this case the application classloader is used.

For example, the following will fail because AttachCurrentThread is used to get the JNIEnv pointer:

static JavaVM* jvm;  // Global variable

void myCallback(void) {
    JNIEnv* env;
    jvm->AttachCurrentThread((void**)&env, NULL);
    // Fails if some/class is not in the application classloader:
    jclass cls = env->FindClass("some/class");
    jmethodID methodID = env->GetMethodID(cls, "methodName",
      "(Ljava/lang/String;)V or whatever signature");
    env->CallVoidMethod(callback, methodID, ...);
    jvm->DetachCurrentThread();
  }
}
A solution is to cache the method ID, for example:

static jmethodID mid;  // Global variable

JNIEXPORT jint JNICALL JNI_OnLoad(JavaVM *vm, void *reserved) {
...
  // Store the JavaVM pointer
    jvm = vm;

  // Find the class and store the method ID
  // Will use the class loader that loaded the JNI library
    jclass cls = env->FindClass(className"some/class");
    if(!cls) goto ERR;

    mid = env->GetMethodID(cls, "methodName",
      "(Ljava/lang/String;)V or whatever signature");
    if(!mid) goto ERR;
...
}

void myCallback(void) {
    JNIEnv* env;
    jvm->AttachCurrentThread((void**)&env, NULL);
    env->CallVoidMethod(callback, mid, ...);
     // Handle error ...
    jvm->DetachCurrentThread();
  }
}
Platform - Ant
Custom Ant tasks and Ant types must be separate from plug-in library JARs

Including the class files for custom Ant tasks or Ant types in the regular code JAR for your plug-in causes problems. These class files must be provided in a separate JAR that is contributed to the org.eclipse.ant.core.antTasks or antTypes extension point (and not declared as a library in the plug-in's manifest). This ensures that the Ant tasks and types are loaded by the special Ant class loader and not by a plug-in classloader. (bug 34466).

XDoclet support from within Eclipse

Since there are differences when running Ant from the commandline and within Eclipse, some extra steps may be needed to have XDoclet support function correctly within Eclipse. Problems may occur creating XDoclet subtasks. The workarounds and full discussion can be found in bug report. (bug 37070)

Ant Editor code completion based on Ant 1.6.x

Code completion provided by the Ant editor does not respect the user-specified version of org.eclipse.ant.core plug-in or ANT_HOME. Code completion proposals are mostly based on Ant 1.6.x with some updates to Ant 1.8.3 (bug bug 193046)

Setting build loggers not supported when debugging Ant builds

When debugging Ant builds within Eclipse, setting -logger as a program argument will be ignored.

Renaming an External Tool builder set to run during auto-build will cause errors

If you rename an existing external tool builder that is configured to run during auto-builds, you will get the following error: Errors during build. Errors running builder "Integrated External Tool Builder" on project <PROJECT_NAME>. The builder launch configuration could not be found. The workaround is to first disable the builder for auto-builds and then rename the builder. (bug 118294)

Slow typing/saving of the Ant editor with imports that define numerous macrodefs

The Ant editor is slow on saving with buildfiles that have <import> declarations of buildfiles that have numerous <macrodef>s. See bug 125117 for a possible workaround

Ant 1.8.x reports missing libraries as build failures

In Ant 1.8.x, if you try to use a task that requires additional libraries and you do not have the libraries on the Ant classpath, the build will now properly report as failed. In previous versions of Ant, the build would still report that it had succeeded even though it actually failed to run any of the tasks from additional bundles. See bug 344518.

For more information on tasks that require additional bundles please refer to the Ant 1.8.2 release notes and the Optional Tasks section in the Ant manual.

Platform - User Assistance
Welcome page not displayed properly (Linux/Unix)

The default Welcome implementation is HTML-based and requires a supported browser in order to work. If no supported browser can be found, Welcome falls back to its Forms-based implementation, which has a different (simpler) appearance. Consult the SWT FAQ for supported browsers and setting up your browser to work with eclipse.

Help browser tool bar buttons do not work for some documents

The Help browser's Print, Synchronize, and Bookmark buttons do not work for pages that are not actually installed with the product. However, you can always use the print command in the browser's context menu to print the page you're reading. (bug 44216)

Help documents not displayed in a browser or very slow document loading (Windows only)

If your LAN settings are not properly configured for local host access, your Help browser might open to a blank page or display an HTTP error instead of a help page, or you may experience long delays when loading help documents. Your system administrator can configure your LAN settings so that help documents can be accessed from the local help server.

In the Control Panel, open Internet Options, select the Connections tab and choose LAN Settings.
If your host was configured to use DHCP for IP assignment, make sure that the "Automatically detect settings" check box is cleared.
If you use a proxy server, ensure that the "Bypass proxy server for local addresses" is selected.
In "Advanced" settings for proxies, add "127.0.0.1;localhost" to the "Exceptions" if these addresses are not listed.
If you are using an automatic configuration script for proxy settings, and are not sure that the script is correct, clear the "Use automatic configuration script" check box.
Working disconnected from the network (Windows only)

If you are experiencing problems when not connected to the network, you must install the loopback adapter from the Windows installation CD. (bug 831)

Using Internet Explorer in offline mode (Windows only)

If you have been using Internet Explorer in Offline mode, when you access the help system you will get a message indicating that the web page you requested is not available offline or a blank page will display. Click Connect or deselect "Work Offline" in the Internet Explorer "File" menu to return the system behavior to normal.

Platform - UI
Dirty state not tracked properly for OLE documents (Windows only)

The dirty state for an OLE document is not updated properly. This causes Eclipse to prompt to save the contents of the editor when the document is closed, even if the contents have already been saved. (bug 2564)

OLE document crashes can cause Eclipse to also crash (Windows only)

If an OLE document crashes, Eclipse can crash, or the workbench menus can become inconsistent.

Toolbars only containing contributed controls exhibit display errors on Mac/Linux

Currently there is no way on the Max or Linux platforms to define the height for controls contributed to toolbars, nor will those platforms respect the size returned by the control's computeSize method. If you encounter this issue there is currently no truly viable workaround. (bug 183003)

Allocating enough memory and solving OutOfMemoryError

The native launcher checks the JVM but PDE launches simply use java and don't go through the native launchers. The workaround is to add the appropriate JVM arg to your launch config or to the Preferences > Java > Installed JREs. (bug 339763)

Capabilities and Activities don't affect the menus and toolbars

Capabilities used to hide GUI elements like menu entries work for commands and individual actionSet entries, but Capabilities have not been fully implemented. (bug 359778)

Platform - SWT
Non-uniform scaling of text vs. icons when using intermediate scaling factors on high-DPI displays

Eclipse automatically scales images on high-DPI monitors based on the resolution of the monitor. However, this scaling works only with integer scaling factors like 100%, 200% etc by default. So, at intermediate scaling factors like 150%, 175% etc., its likely that the icons and text are scaled differently as the text scaling is handled directly by the operating system.

Eclipse plug-in based on the SWT Browser throws exception

The SWT Browser widget uses a platform-specific web browser to render HTML. The org.eclipse.swt.SWTError exception ("No more handles") is thrown on platforms that don't meet the requirements for running the Browser widget. Supported platforms and prerequisites are listed on the SWT FAQ item "Which platforms support the SWT Browser?".

SWT Browser widget does not work on some recent Linux versions e.g Ubuntu 16.04

Recent Linux versions such as Ubuntu 16.04 do not have the Webkit libraries installed by default, causing the instantiation of the Browser widget to fail. This means that the Internal Web Browser view in Eclipse won't be usable. When running with GTK3, even the Welcome page appears blank (bug 492379). The workaround is to install the Webkit libraries (libwebkitgtk-3.0 for GTK3 and libwebkitgtk-1.0 for GTK2) from the OS package repositories before using Eclipse and/or SWT.

SWT Browser widget crashes when run with XULRunner 31 run-time (Linux only)

Creation of an SWT Mozilla style Browser with XULRunner 31 run-time crashes on Linux. The workaround is to use other supported XULRunner run-times or webkit which is the default on Linux. (bug 467646)

IME conversion problem (Solaris GTK only)

When typing Japanese text, the conversion to Kanji must be done one ideogram at a time. (bug 226636)

Eclipse won't start (Linux GTK PPC only)

Eclipse fails to create a lock file with reason "No locks available". To launch eclipse you must disable file locking using the osgi.locking property. For example, you could launch eclipse as follows: 
eclipse -vmargs -Dosgi.locking=none

Eclipse icon is duplicated in task-bar on Windows

Workaround is to pin the launched eclipse application and not the launcher, for more details, refer bug 314805

BIDI Segments in Text controls

BIDI Segments in Text controls only work on Windows and GTK. (bug 388578)

Block Selection functionality provided by StyledText is not BIDI aware

When the orientation of characters under the left and right edges of the block selection rectangle are not the same, the actual selection ranges (in memory) differ from the visual representation of the selection. (bug 277929)

Platform - Team - CVS
The following are known problems with the CVS repository provider only, and do not apply to other repository providers. Additional information on how to use CVS from Eclipse can be found in the Eclipse CVS FAQ.

CVS server compatibility

The CVS plug-in parses messages returned from the CVS server. If the format of these messages is not as expected, some of the plug-in's functionality may be missing. The CVS plug-in is compatible with all stable 1.11.X builds of the CVS server, and should be compatible with future releases in that stream unless text message formats change (the last tested server was 1.11.22). As for the 1.12.X feature releases of CVS, the Eclipse CVS client has been tested with builds up to 1.12.13. However, future releases could easily break the Eclipse CVS client. Basic functionality, such as Checkout, Commit, and Update, should always work, but there may be problems with more advanced commands such as Synchronizing and Browsing the repository.

Connection cannot be found after initially missing

If a connection initially fails due to a network problem, the connection may continue to fail even when the network problem is fixed. In order to establish the connection you must exit and restart Eclipse. (bug 9295)

Received broken pipe signal" error from server

Eclipse sometimes performs multiple commands within a single connection to the server. This may cause problems with CVS servers that are running server scripts in response to certain commands. (bugs 23575 and 23581)

Terminated with fatal signal 10" error from server

There is a bug in the CVS server related to some compression levels. If you get this error, changing the compression level on the CVS preference page may help. (bug 15724)

error using ext connection method

There are a few situations that can result in an "Unknown response" error messages when using the ext connection method. One situation involves using an external communications client (e.g. rsh or ssh) that adds CRs to the communications channel (bug 21180). Another involves Eclipse not properly reading the stderr output of the external communications tool. (bug 11633)

A disabled CVS capability may not be auto-enabled in existing workspaces

New in 3.0 is the ability to disable capabilities and the CVS support in Eclipse can be disabled. However, for backwards compatibility the CVS capability is auto-enabled in existing workspaces that already contain CVS projects. The auto-enabling function may not run if the team support plugin is not loaded at startup. (bug 66977)

Builder output files may appear as changed

When folders containing build output are shared they may get improperly marked as dirty when build output is generated.

Enabling GNOME proxy support

GNOME applications can make use of proxy settings defined in this environment. If set, Eclipse will use it prior to proxy settings declared using env variables. This feature is disabled by default, to enable it launch Eclipse with "-Dorg.eclipse.core.net.enableGnome" switch. That is,

    eclipse -Dorg.eclipse.core.net.enableGnome
  
Platform - Install/Update
Manually installing features and plug-ins on a FAT file system (Windows only)

When features and plug-ins are manually installed on top of an Eclipse-based product install located on a FAT file system that has already been run at least once, the product must be explicitly restarted with -clean. That is,

    eclipse.exe -clean
  
Connecting to untrusted sites using https

You cannot install or update software from a site using https whose certificate is not chained to a trusted root certificate in your local certificate store. This typically means the server is using a self-signed certificate, or a certificate authenticated by an unknown third party.

Extension location is lost if the install path changes

A previously configured extension location may be temporarily removed if the install is moved or mounted under a different path. This only happens when the link file that configures the extension location uses a relative path that points to a directory under the Eclipse install. On a second startup using the same install path, the extension location is added again (bug 95403).

Java development tools (JDT)
Multiple regions formatting in a given source snippet

In version 3.4, the API method org.eclipse.jdt.core.formatter.CodeFormatter.format(int, String, IRegion[], int, String) was added to allow the formatting of several regions in a source snippet with a single pass. 
Even if specified, this method does not currently accept comments of the following kinds:

org.eclipse.jdt.core.formatter.CodeFormatter#K_SINGLE_LINE_COMMENT
org.eclipse.jdt.core.formatter.CodeFormatter#K_MULTI_LINE_COMMENT
org.eclipse.jdt.core.formatter.CodeFormatter#K_JAVA_DOC
This will be fixed in a future release (bug 233967).

Searching for constant field references

Search does not find references to constant fields inside binaries because the Java Language Specification mandates that constant field values be inlined in the class file's byte codes, leaving no trace of a field reference. (bug 12044)

Cut, copy, paste not working for linked resources in views showing Java elements

The cut, copy, and paste actions do not work for linked files and folders appearing in views that show Java elements, including the Package Explorer. The workaround is to use these actions from the Navigator view instead. (bug 34568)

Java working sets not working correctly for elements from JRE system library container

Applying a working set consisting entirely of elements from the JRE System library container as a filter to the packages view might result in an empty Package Explorer. (bug 30442)

Breakpoints unreliable running Sun 1.6.0_14

Developers debugging applications on Sun's 1.6.0_14 virtual machine should be aware that breakpoints are unreliable (i.e. do not always suspend execution). The problem occurs on Windows and Linux platforms. This is an issue with the VM and not with Eclipse. The workaround is to use the -XX:+UseParallelGC VM option. (bug 279137).

Suspend on uncaught exception overrides exception breakpoint location filters

Exception breakpoints can be configured with location filters (inclusive and exclusive). When an unchecked exception is configured to not suspend execution in a specific class, execution will still suspend when the user preference to suspend on uncaught exceptions is on. (bug 66770)

Running Java programs with non-Latin-1 characters in package or class names

You get a java.lang.NoClassDefFoundError when running Java programs with non-Latin characters in the package or class names. The workaround is to package the class files as a JAR file and run the program out of the JAR and not from the file system directly. (bug 4181)

Cannot run or debug class in a project with GB18030 characters in project name

Most class libraries do not properly support the creation of a system process (via java.lang.Runtime.exec(...) ) when the specified command line contains GB18030 characters. This limitation means the debugger cannot launch applications when the command line it generates contains GB18030 characters. (bug 32206)

Cannot detect installed JRE with GB18030 characters in path name

Automatic JRE detection fails when the JRE is stored in a directory containing GB18030 characters in its name. (bug 33844)

Cannot generate Javadoc for packages with GB18030 characters in the name

Most class libraries do not properly support the creation of a system process (via java.lang.Runtime.exec(...) ) when the specified command line contains GB18030 characters. Since Javadoc is created using the Javadoc executable provided with the JDK, generating Javadoc fails if the package or class name contains GB18030 characters. (bug 32215)

Unable to debug stack overflows

If a debug session suspends on a java.lang.StackOverflowError exception (due to an exception breakpoint), the debugger may not be able to retrieve any debug information from the target JVM. As well, the debugger may not be able to reliably interact with the target JVM past this point. (bug 19217)

Evaluation limitation

The debugger uses threads in the target JVM to perform evaluations (both explicit evaluations that the user requests, and implicit evaluations such as toString() invocations in the Variables view). The Java Debug Interface (JDI) requires that the thread in which an evaluation is performed be suspended by a user event (that is, a breakpoint or step request). Evaluations cannot be performed on threads suspended by the suspend action. As well, when a breakpoint is configured to suspend the JVM rather than just the individual thread, the threads which did not encounter the breakpoint are not in a valid state to perform an evaluation. When an evaluation is attempted in a thread that is not in a valid state to perform an evaluation, an error message will appear to the effect of "Thread must be suspended by step or breakpoint to perform method invocation". (bug 34440)

Missing debug attributes

The debugger requires that class files be compiled with debug attributes if it is to be able to display line numbers and local variables. Quite often, class libraries (for example, " rt.jar ") are compiled without complete debug attributes, and thus local variables and method arguments for those classes are not visible in the debugger.

Using Hot Code Replace

Hot code replace is supported on JDK 1.4.x VMs, and IBM J9 VMs. The debugger will attempt to replace all class files that change in the workspace as the user edits and builds source code. However, hot code replace is limited to changes that a particular virtual machine implementation supports. For example, changes within existing methods may be supported, but the addition or removal of members may not be.

Scrapbook

Setting a breakpoint inside a scrapbook page is not supported.

When a snippet is run in the scrapbook which directly or indirectly calls System.exit(int) , the evaluation cannot be completed, and will result in a stack trace for a com.sun.jdi.VMDisconnectedException being displayed in the scrapbook editor.

Terminating a scrapbook page while it is performing an evaluation results in a com.sun.jdi.VMDisconnectedException being displayed in the scrapbook editor.

Debugging over slow connections

A global Java debug preference specifies the debugger timeout, which is the maximum amount of time the debugger waits for a response from the target VM after making a request of that VM. Slow connections may require that this value be increased. The timeout value can be edited from the Java > Debug preference page. Changing the timeout value only affects subsequently launched VM, not VMs that are already running.

Updating of inspected values

When inspecting the result of an evaluated expression in the debugger, it is important to note that the result displayed is the result of that expression at the time it was evaluated. For example, when inspecting a simple integer counter (primitive data type), the value displayed in the Expressions view is the value when the expression was evaluated. As the counter is changed in the running program, the inspected result will not change (since the view is not displaying the value bound to a variable - it is displaying the value of an expression, and the value of a primitive data type cannot change). However, if an expression results in an object, fields of that object will be updated in the inspector as they change in the running program (since the value bound to fields in an object can change).

Stepping over native methods that perform I/O

When the debugger steps over native methods that perform I/O to System.out or System.err, the output may not appear immediately unless the native performs a flush on the output buffer.

VM and process termination running on IBM 1.3 JVM on Linux (Linux only)

Terminating a launch, debug target, or system process associated with a debug target running on the IBM 1.3 JVM on the Linux platform does not work when the associated debug target has a suspended thread. To remove such debug targets from the debug UI, select Terminate and Remove from the debug view's pop-up menu (or use the shortcut "delete" key). Associated system processes in the OS may not be properly cleaned up. If a debug target has no suspended threads, termination works properly. (bug 1631)

Java Annotation Processing

Some methods in the processing API are unimplemented when compiling within the IDE, and will throw UnsupportedOperationException .

Java 6 annotation processors are supported in the batch compiler and in the IDE. By design, Java 6 processors are only executed during a build, not while editing. (bug 188558)

Java 5 annotation processors are supported in the IDE only. Java 5 processors can be executed while editing, as well as during a build. Slow annotation processors can cause a slowdown of the editing experience. If this occurs, you may wish to turn off Enable processing in editor on the Java Compiler > Annotation Processing properties page of your Java project.

Java indexing encounters problems when a folder is used both as a source and a class folder

Java indexing encounters problems when a folder is used both as a source folder in a project and as a class folder in another project. Hence, when this peculiar setup is used, the Java Search might miss matches located in such a folder. To avoid this kind of problem, it is strongly advised to use different folders for sources and binary classes. (bug 309903)

Plug-in Development Environment (PDE)
Feature manifest editor does not preserve all comments

When a non-source page of the feature manifest editor is used, PDE will convert changes back into XML by regenerating the file. Although the overall content and most of the comments are preserved, some comments may be lost. (bug 59502)

PDE will not unzip source zips of some plug-ins

In the plug-in import wizard, when you choose to import plug-ins as "projects with source folders", PDE will not unzip the source for the org.apache.ant. This is because the source ZIPs contains code that will not compile when unzipped as it requires additional JARs that are not part of the SDK. To avoid the creation of plug-in projects that won't compile, PDE will import these plug-ins as binary and attach source, so you would still be able to read the source, you just won't be able to modify it. Also, PDE will not unzip the source for the org.eclipse.swt plug-ins. In this case, it is because, when shipped, the swt code is spread across a plug-in and a fragment, and when unzipped, it will require circular dependencies between the plug-in and fragment projects. These circular dependencies are at minimum marked as warnings by the JDT compiler and may result in unpredictable build behavior. Therefore, PDE always imports org.eclipse.swt as binary with source attached. (bug 66314)

Emacs key bindings do not work in manifest editor fields

Non-default key bindings currently do not work in fields on non-source pages of the PDE manifest editors. (bug 19482)

Export of plug-in may silently drop classes

When exporting a plug-in using the plug-in, feature or product wizards, some classes might be dropped from the resulting archive if their fully qualified name is too long. This typical path limitation can be worked around by creating the jar of the problematic plug-in by using the Jar export wizard. (bug 97150)

Compilation errors when exporting projects not stored outside of the workspace

When exporting multiple plug-ins and one is stored outside of the workspace, compile errors occurs on export. To work around the problem, you can either export the plug-ins one by one, or change their location. (bug 98579)

Headless build needs to be run from a fully qualified path

When running a headless build using the scripts provided by pde build, the properties builder and buildDirectory must refer to a fully qualified path. (bug 139554)

Importing in Eclipse application fails if plug-in exists in host workspace

When running an Eclipse application (self-hosting) importing plug-ins will not work correctly if the plug-in being imported exists in the host Eclipse's workspace. This is because PDE modifies the target platform of the application to point at the running plug-ins from the host (target weaving). This also affects the PDE test suite. (bug 294005)

Reusing a workspace after changing architectures silently breaks PDE models

If a workspace is reused on a machine with a different architecture, the PDE models used to build plug-ins may silently fail. To work around this problem, delete the metadata in <workspace>/.metadata/.plugins/org.eclipse.pde.core. (bug 350172)

Missing @since tag API Tools problems on interface fields containing @noreference tag

The Eclipse platform 4.6 release will not allow the API Tools @noreference tag on interface fields. This was changed because all interface fields are constant fields that cannot support the @noreference restriction. The tag was allowed in previous releases and this usage will now be considered an API change requiring a @since tag. It is recommended that you create an API Tools filter for the missing @since tag problem. This filter can be removed as soon as the API baseline has been regenerated. (bug 402393)

Running Eclipse
After installing the Eclipse SDK in a directory, you can start the Workbench by running the Eclipse executable included with the release (you also need a Java SE 8 JRE, not included with the Eclipse SDK). On Windows, the executable file is called eclipse.exe , and is located in the eclipse sub-directory of the install. If installed at c:\eclipse-SDK-4.6-win32 , the executable is c:\eclipse-SDK-4.6-win32\eclipse\eclipse.exe . Note: Set-up on most other operating environments is analogous. Special instructions for Mac OS X are listed below.

Allocating enough memory and solving OutOfMemoryErrors
By default, Eclipse will allocate up to 1024 megabytes of Java heap memory. This should be ample for all typical development tasks. However, depending on the JRE that you are running, the number of additional plug-ins you are using, and the number of files you will be working with, you could conceivably have to increase this amount. Eclipse allows you to pass arguments directly to the Java VM using the -vmargs command line argument, which must follow all other Eclipse specific arguments. Thus, to increase the available heap memory, you would typically use:

eclipse -vmargs -Xmx<memory size>
with the <memory size> value set to greater than "1024M" (1024 megabytes -- the default).

Note that setting memory sizes to be near or larger than the amount of available physical memory on your machine will cause Java to "thrash" as it copies objects back and forth to virtual memory, which will severely degrade your performance.

Selecting a workspace
When the Workbench is launched, the first thing you see is a dialog that allows you to select where the workspace will be located. The workspace is the directory where your work will be stored. If you do not specify otherwise, Eclipse creates the workspace in your user directory. This workspace directory is used as the default content area for your projects as well as for holding any required metadata. For shared or multi-workspace installs you must explicitly specify the location for your workspace using the dialog (or via the " -data " command line argument).

Specifying the Java virtual machine
Here is a typical Eclipse command line:

eclipse -vm c:\jdk6u22\jre\bin\javaw
Tip: It's generally a good idea to explicitly specify which Java VM to use when running Eclipse. This is achieved with the " -vm " command line argument as illustrated above. If you don't use " -vm ", Eclipse will look on the OS path. When you install other Java-based products, they may change your path and could result in a different Java VM being used when you next launch Eclipse.

To create a Windows shortcut to an installed Eclipse:

Navigate to eclipse.exe in Windows Explorer and use Create Shortcut on the content menu.
Select the shortcut and edit its Properties. In the Target: field append the command line arguments.
Opening this shortcut launches Eclipse. (You can drag the shortcut to the Windows Desktop if you want to keep it in easy reach.)

Mac OS X
On Mac OS X, you start Eclipse by double clicking the Eclipse application. If you need to pass arguments to Eclipse, you'll have to edit the eclipse.ini file inside the Eclipse application bundle: select the Eclipse application bundle icon while holding down the Control Key. This will present you with a popup menu. Select "Show Package Contents" in the popup menu. Locate eclipse.ini file in the Contents/Eclipse sub-folder and open it with your favorite text editor to edit the command line options.

On MacOS X you can only launch a UI program more than once if you have separate copies of the program on disk. The reason for this behavior is that every UI application on Mac can open multiple documents, so typically there is no need to open a program twice. Since Eclipse cannot open more than one workspace, this means you have to make a copy of the Eclipse install if you want to open more then one workspace at the same time (bug 139319).

If you need to launch Eclipse from the command line, you can create a symbolic link such as "eclipse". It should point to the eclipse executable inside the application bundle and takes the same arguments as "eclipse.exe" on other platforms.

On Mac OS X 10.4 and later, you may notice a slow down when working with significant numbers of resources if you allow Spotlight to index your workspace. To prevent this, start System Preferences, select the Spotlight icon, then the Privacy tab, then click the Add button ("+") and find your workspace directory in the dialog that appears.

Shared Install
The startup speed of a shared install can be improved if proper cache information is stored in the shared install area. To achieve this, after unzipping Eclipse distribution, run Eclipse once with the "-initialize" option from an account that has a write access to the install directory. See shared installs in Eclipse Help for more information.

Special Operating System Requirements
AIX

In order for this version of Eclipse to run, GTK2 libraries and their dependencies must be installed and available on the library path (i.e. LIBPATH). These libraries are available from http://www-03.ibm.com/systems/power/software/aix/linux/toolbox/alpha.html . To determine the full set of libraries required for installation, download and install the "gtk2" rpm, at which time any missing dependencies will be listed. These dependencies should be available for download from the same site.

You can run Eclipse using the VM from IBM Developer Kit for AIX versions 5 and 6. The IBM Developer Kit for AIX can be downloaded from: https://www.ibm.com/developerworks/java/jdk/aix/service.html

HPUX

In order for this version of Eclipse to run, GTK2 libraries must be installed and available on the library path (i.e. LD_LIBRARY_PATH).

For example, export LD_LIBRARY_PATH=/opt/gtk_64bit/lib/hpux64/

To prevent errors with workspace locking, the following command line should be used to launch Eclipse:

"eclipse -vmargs -Dosgi.locking=none"

Otherwise, eclipse may fail to create a lock file with reason: "Workspace in use or cannot be created, choose a different one."

Note you might also need to add the -d64 option to the vmargs when using an Oracle JRE. (-vmargs -d64)

Solaris

This version of Eclipse works best with the latest version of GTK+ 2, but the earliest version supported is GTK+ 2.10.

The GTK libraries must be available on the library path (i.e. LD_LIBRARY_PATH).

Upgrading Workspace from a Previous Release
Users who don't use "-data"
If you weren't previously using "-data" to specify your workspace, follow these steps to upgrade:

Find the workspace directory used by your old version of Eclipse. Typically this is located inside the directory in which Eclipse was installed in a sub-directory called "workspace". If you are using a shortcut or script to launch Eclipse, then it will be under the current working directory of that shortcut or script in a sub-directory called "workspace". For Windows users, this is specified by the "Start in:" argument in your shortcut properties.
Copy this workspace directory to a new, empty location outside of any Eclipse install directory.
Install the new version of Eclipse in a new location, separate from any old version of Eclipse.
If you had installed additional features and plug-ins into your old Eclipse, you should re-install them in the new Eclipse.
Start this new version of Eclipse and select this location using the workspace chooser dialog at startup (or use "-data" command line argument to pre-select the workspace location).
Users who do use "-data"
If you were previously using the " -data " argument to start Eclipse, your upgrade path is much easier:

Optionally copy your workspace directory to a new, empty location outside of any Eclipse install directory as a backup.
Install the new version of Eclipse in a new location, separate from any old versions of Eclipse.
If you had installed additional features and plug-ins into your old Eclipse, you should re-install them in the new Eclipse.
Start this new version of Eclipse and select this location using the workspace chooser dialog at startup (or use "-data" command line argument to pre-select the workspace location).
Note: Copying your workspace is recommended because, after you've upgraded your workspace, you won't be able to use it again with an older version of Eclipse. If you ever want to go "back in time" to an earlier release, you will need that backup.

Users who use User Libraries or classpath containers that contain JARs referencing other libraries via Class-Path in the MANIFEST.MF
If you want the referenced JAR files to be included in the classpath, you can do one of the following:

Add the system property (-DresolveReferencedLibrariesForContainers=true) to the -vmargs list on start-up, or
Manually add the referenced JARs to the User Library or to the project.
Dropped in bundles may not resolve after upgrade
If you have installed bundles by dropping them into the plugins or dropins directory, they might no longer resolve when you upgrade to a new Eclipse Platform version. In each new version of the Eclipse Platform, there are new versions of bundles included in the platform, and often a small number of removed bundles. This may cause your previously dropped in bundles to no longer resolve and run. It is always recommended that you install software via the Help > Install New Software mechanism so you are made aware of any install-time failure to resolve dependencies.

Interoperability with Previous Releases
Interoperability of Release 4.6 with previous releases
Sharing projects between heterogeneous Eclipse 4.6 and 4.5

Special care is required when a project in a team repository is being loaded and operated on by developers using Eclipse-based products based on different feature or plug-in versions. The general problem is that the existence, contents, and interpretation of metadata files in the workspaces may be specific to a particular feature or plug-in version, and differ between versions. The workspace compatibility guarantees only cover cases where all developers upgrade their Eclipse workspaces in lock step. In those cases there should be no problem with shared metadata. However, when some developers are working in Eclipse 4.6 while others are working in Eclipse 3.x, there are no such guarantees. This section provides advice for what to do and not to do. It addresses the specific issues with the Eclipse SDK.

The typical failure mode is noticed by the 4.6 user. 4.6 metadata is lost when a 4.5 user saves changes and then commits the updated metadata files to the repository. Here's how things typically go awry:

A user working in Eclipse 4.6 creates or modifies a project in a way that results in changes to a shared metadata file that rely on 4.6-specific information. The user then commits the updated project files, including the shared metadata file, to the shared repository.
Another user working in Eclipse 4.5 or earlier shares this project from the same repository. The 4.6-specific information in the shared metadata file is not understood by Eclipse 4.5, and is generally discarded or ignored without warning. The user modifies the project in a way that results in changes to the shared metadata file, causing the shared metadata file to be rewritten without any of the 4.6-specific information. The user commits the updated project files, including the shared metadata file, to the shared repository. The user is generally unaware that shared information has just been lost as a result of their actions.
A user working in Eclipse 4.6 picks up the changes to a project from the shared repository, including the updated shared metadata file. The user may be unaware that they have just taken a retrograde step until later when things start to malfunction.
Here are some things to watch out for when sharing projects between Eclipse 4.6 and earlier releases:

Virtual folders - Eclipse 4.6 supports a notion of virtual folders that did not exist in Eclipse 3.5 or earlier. If such virtual folders are created in 4.6, and the project is subsequently loaded into an Eclipse 3.5 or earlier workspace, these folders will not be recognized. Recommendation: avoid creating virtual folders where project compatibility with Eclipse 3.5 or earlier is required.
Resource filters - Eclipse 4.6 supports a notion of resource filters that did not exist in Eclipse 3.5 or earlier. If such filters are added to resources in 4.6, and the project is subsequently loaded into an Eclipse 3.5 or earlier workspace, these filters will not be recognized. Recommendation: avoid creating resource filters where project compatibility with Eclipse 3.5 or earlier is required.
Predefined path variables - Eclipse 4.6 supports a set of built in path variables that can be used as the basis for linked resource locations. Such variables will not be defined automatically in Eclipse 3.5 or earlier. If compatibility with 3.5 or earlier workspace is required, users on 3.5 or earlier workspaces will need to define such path variables manually.
Using Eclipse 4.6 to develop plug-ins that work in Eclipse 4.5

It is also possible (and reasonable) to use Eclipse 4.6 to develop a plug-in intended to work in Eclipse 4.5 or earlier. Use the Plug-in Development > Target Platform preference page to locate non-workspace plug-ins in an Eclipse 4.5 install. This ensures that the code for your plug-in is being compiled and tested against Eclipse 4.5 APIs, extension points, and plug-ins. (The above list of concerns do not apply since they affect the layout and interpretation of files in the plug-in project but none affect the actual deployed form of the plug-in.)

Appendix: Execution Environment by Bundle
See the table in the Eclipse 4.6 Project Plan Appendix for the list of the minimum execution environment (Java class library) requirements of each bundle.

Sun, Solaris, Java and all Java-based trademarks are trademarks of Oracle Corporation. in the United States, other countries, or both.

IBM is a trademark of International Business Machines Corporation in the United States, other countries, or both.

Microsoft, Windows, Windows NT, Vista, and the Windows logo are trademarks of Microsoft Corporation in the United States, other countries, or both.

Apple and Mac OS are trademarks of Apple Computer, Inc., registered in the U.S. and other countries.

Other company, product, and service names may be trademarks or service marks of others.

(c) Copyright Eclipse Contributors 2009, 2016
