Step 1 – Install Java OpenJDK

By default, Java OpenJDK is available in the Fedora default repo. You can search the list of all available Java versions using the following command.

dnf search openjdk

You should see the following output.

java-1.8.0-openjdk-openjfx-slowdebug.x86_64 : OpenJDK x OpenJFX connector for packages with debugging on and no optimisation. his package adds symliks finishing Java FX
                                            : integration to java-1.8.0-openjdk-slowdebug
java-1.8.0-openjdk-slowdebug.x86_64 : OpenJDK 8 Runtime Environment unoptimised with full debugging on
java-1.8.0-openjdk-src.x86_64 : OpenJDK 8 Source Bundle
java-1.8.0-openjdk-src-fastdebug.x86_64 : OpenJDK 8 Source Bundle for packages with debugging on and optimisation
java-1.8.0-openjdk-src-slowdebug.x86_64 : OpenJDK 8 Source Bundle for packages with debugging on and no optimisation
java-11-openjdk.x86_64 : OpenJDK 11 Runtime Environment
java-11-openjdk.i686 : OpenJDK 11 Runtime Environment
java-11-openjdk-demo.x86_64 : OpenJDK 11 Demos
java-11-openjdk-demo-fastdebug.x86_64 : OpenJDK 11 Demos optimised with full debugging on
java-11-openjdk-demo-slowdebug.x86_64 : OpenJDK 11 Demos unoptimised with full debugging on
java-11-openjdk-devel.i686 : OpenJDK 11 Development Environment
java-11-openjdk-devel.x86_64 : OpenJDK 11 Development Environment
java-latest-openjdk-src-slowdebug.x86_64 : OpenJDK 18 Source Bundle for packages with debugging on and no optimisation
java-latest-openjdk-static-libs.x86_64 : OpenJDK 18 libraries for static linking
java-latest-openjdk-static-libs-fastdebug.x86_64 : OpenJDK 18 libraries for static linking optimised with full debugging on
java-latest-openjdk-static-libs-slowdebug.x86_64 : OpenJDK 18 libraries for static linking unoptimised with full debugging on
openjdk-asmtools-javadoc.noarch : Javadoc for openjdk-asmtools
======================================================================== Name Matched: openjdk =========================================================================
openjdk-asmtools.noarch : To develop tools create proper & improper Java '.class' files
======================================================================= Summary Matched: openjdk =======================================================================
icedtea-web.x86_64 : Additional Java components for OpenJDK - Java Web Start implementation

You can now install specific versions of Java OpenJDK to your system easily.

For example, to install Java 11, run the following command.

dnf install java-11-openjdk -y

You can verify the Java version using the following command.

java --version

Output.

openjdk 11.0.15 2022-04-19
OpenJDK Runtime Environment 18.9 (build 11.0.15+10)
OpenJDK 64-Bit Server VM 18.9 (build 11.0.15+10, mixed mode, sharing)

If you want to install Java 8, run the following command.

dnf install java-1.8.0-openjdk.x86_64 -y

To install the latest Java version, run the following command.

dnf install java-latest-openjdk -y

Step 2 – Set Java Default Version

Java allows you to switch between multiple Java versions easily. Run the following command to set a default Java version.

alternatives --config java

You will be asked to set the default Java version as shown below.

There are 3 programs which provide 'java'.

  Selection    Command
-----------------------------------------------
*+ 1           java-11-openjdk.x86_64 (/usr/lib/jvm/java-11-openjdk-11.0.15.0.10-1.fc34.x86_64/bin/java)
   2           java-1.8.0-openjdk.x86_64 (/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.332.b09-1.fc34.x86_64/jre/bin/java)
   3           java-latest-openjdk.x86_64 (/usr/lib/jvm/java-18-openjdk-18.0.1.0.10-1.rolling.fc34.x86_64/bin/java)

Enter to keep the current selection[+], or type selection number: 3

Type 3 and press the Enter key to set the Java latest version as the default version.

Now, verify the newly set Java version with the following command.

java --version

You will get the following.

openjdk 18.0.1 2022-04-19
OpenJDK Runtime Environment 22.3 (build 18.0.1+10)
OpenJDK 64-Bit Server VM 22.3 (build 18.0.1+10, mixed mode, sharing)

Step 3 – Install Oracle Java

To install Oracle Java, login to the Oracle Java website and download Java 16 to your server.

After downloading Java 16, copy it to the Java directory.

cp jdk-16.0.2_linux-x64_bin.tar.gz /usr/local/java

Next, navigate to the Java directory and extract the downloaded file.

cd /usr/local/java
tar xvzf jdk-16.0.2_linux-x64_bin.tar.gz

Next, edit the profile file and define the location of Java 16.

nano /etc/profile

Add the following lines.

JAVA_HOME=/usr/local/java/jdk-16.0.2
PATH=$PATH:$HOME/bin:$JAVA_HOME/bin
export JAVA_HOME
export PATH

Save and close the file, then reload the Java environment variable using the following command.

source /etc/profile

Next, set Oracle Java 16 as the default version with the following command.

update-alternatives --install "/usr/bin/java" "java" "/usr/local/java/jdk-16.0.2/bin/java" 1
update-alternatives --set java /usr/local/java/jdk-16.0.2/bin/java

Now, check the Java version with the following command.

java --version

You should see the Java version in the following output.

java 16.0.2 2021-07-20
Java(TM) SE Runtime Environment (build 16.0.2+7-67)
Java HotSpot(TM) 64-Bit Server VM (build 16.0.2+7-67, mixed mode, sharing)
