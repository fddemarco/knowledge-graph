---
base: "[[Reading List.base]]"
Category:
  - Code Design
  - Management
  - DevOps
Author: David Farley
Status: In Progress
---
## 12 Factor - Heroku

[https://12factor.net/](https://12factor.net/)

An app’s config is everything that is likely to vary between deploys (staging, production, developer environments, etc). This includes:

- Resource handles to the database, Memcached, and other backing services
- Credentials to external services such as Amazon S3 or Twitter
- Per-deploy values such as the canonical hostname for the deploy

Apps sometimes store config as constants in the code. This is a violation of twelve-factor, which requires strict separation of config from code. Config varies substantially across deploys, code does not.

A litmus test for whether an app has all config correctly factored out of the code is whether the codebase could be made open source at any moment, without compromising any credentials.

Note that this definition of “config” does not include internal application config, such as config/routes.rb in Rails, or how code modules are connected in Spring. This type of config does not vary between deploys, and so is best done in the code.

Another approach to config is the use of config files which are not checked into revision control, such as config/database.yml in Rails. This is a huge improvement over using constants which are checked into the code repo, but still has weaknesses: it’s easy to mistakenly check in a config file to the repo; there is a tendency for config files to be scattered about in different places and different formats, making it hard to see and manage all the config in one place. Further, these formats tend to be language- or framework-specific.

The twelve-factor app stores config in environment variables (often shortened to env vars or env). Env vars are easy to change between deploys without changing any code; unlike config files, there is little chance of them being checked into the code repo accidentally; and unlike custom config files, or other config mechanisms such as Java System Properties, they are a language- and OS-agnostic standard.

Another aspect of config management is grouping. Sometimes apps batch config into named groups (often called “environments”) named after specific deploys, such as the development, test, and production environments in Rails. This method does not scale cleanly: as more deploys of the app are created, new environment names are necessary, such as staging or qa. As the project grows further, developers may add their own special environments like joes-staging, resulting in a combinatorial explosion of config which makes managing deploys of the app very brittle.

In a twelve-factor app, env vars are granular controls, each fully orthogonal to other env vars. **They are never grouped together as “environments”, but instead are independently managed for each deploy**. This is a model that scales up smoothly as the app naturally expands into more deploys over its lifetime.

## Google SRE

[https://sre.google/sre-book/release-engineering/](https://sre.google/sre-book/release-engineering/)

Configuration management is one area of particularly close collaboration between release engineers and SREs. Although configuration management may initially seem a deceptively simple problem, configuration changes are a potential source of instability. As a result, our approach to releasing and managing system and service configurations has evolved substantially over time. Today we use several models for distributing configuration files, as described in the following paragraphs. All schemes involve storing configuration in our primary source code repository and enforcing a strict code review requirement.

**Use the mainline for configuration.** This was the first method used to configure services in Borg (and the systems that pre-dated Borg). Using this scheme, developers and SREs modify configuration files at the head of the main branch. The changes are reviewed and then applied to the running system. As a result, binary releases and configuration changes are decoupled. While conceptually and procedurally simple, this technique often leads to skew between the checked-in version of the configuration files and the running version of the configuration file because jobs must be updated in order to pick up the changes.

**Include configuration files and binaries in the same MPM package. **For projects with few configuration files or projects where the files (or a subset of files) change with each release cycle, the configuration files can be included in the MPM package with the binaries. While this strategy limits flexibility by binding the binary and configuration files tightly, it simplifies deployment, because it only requires installing one package.

**Package configuration files into MPM "configuration packages." **We can apply the hermetic principle to configuration management. Binary configurations tend to be tightly bound to particular versions of binaries, so we leverage the build and packaging systems to snapshot and release configuration files alongside their binaries. Similar to our treatment of binaries, we can use the build ID to reconstruct the configuration at a specific point in time.

For example, a change that implements a new feature can be released with a flag setting that configures that feature. By generating two MPM packages, one for the binary and one for the configuration, we retain the ability to change each package independently. That is, if the feature was released with a flag setting of first_folio but we realize it should instead be bad_quarto, we can cherry pick that change onto the release branch, rebuild the configuration package, and deploy it. This approach has the advantage of not requiring a new binary build.

We can leverage MPM’s labeling feature to indicate which versions of MPM packages should be installed together. A label of much_ado can be applied to the MPM packages described in the previous paragraph, which allows us to fetch both packages using this label. When a new version of the project is built, the much_ado label will be applied to the new packages. Because these tags are unique within the namespace for an MPM package, only the latest package with that tag will be used.

**Read configuration files from an external store.** Some projects have configuration files that need to change frequently or dynamically (i.e., while the binary is running). These files can be stored in Chubby, Bigtable, or our source-based filesystem [Yor11].

In summary, **project owners consider the different options for distributing and managing configuration files and decide which works best on a case-by-case basis.**

## Continuous Delivery

[https://continuousdelivery.com/](https://continuousdelivery.com/)

All of the practices that we discuss to reduce your software’s cycle time and increase its quality, from continuous integration and automated testing to push-button deployments, depend on having everything related to your project in a version control repository.

The objective is to have everything that can possibly change at any point in the life of the project stored in a controlled manner. This allows you to recover an exact snapshot of the state of the entire system, from development environment to production environment, at any point in the project’s history. It is even helpful to keep the configuration files for the development team’s development environments in version control since it makes it easy for everyone on the team to use the same settings. Analysts should store requirements documents. Testers should keep their test scripts and procedures in version control. Project managers should save their release plans, progress charts, and risk logs here. In short, every member of the team should store any document or file related to the project in version control.

In addition to storing source code and configuration information, many projects also store binary images of their application servers, compilers, virtual machines, and other parts of their toolchain in version control. This is fantastically useful, speeding up the creation of new environments and, even more importantly, ensuring that base configurations are completely defined, and so known to be good. One thing that we don’t recommend that you keep in version control is the binary output of your application’s compilation.

In our experience, it is an enduring myth that configuration information is somehow less risky to change than source code. Our bet is that, given access to both, we can stop your system at least as easily by changing the configuration as by changing the source code. If we change the source code, there are a variety of ways in which we are protected from ourselves; the compiler will rule out nonsense, and automated tests should catch most other errors. On the other hand, most configuration information is free-form and untested. In most systems there is nothing to prevent us from changing a URI from “http://www.asciimation.co.nz/” to “this is not a valid URI.” Most systems won’t catch a change like this until run time—at which point, instead of enjoying the ASCII version of Star Wars, your users are presented with a nasty exception report because the URI class can’t parse “this is not a valid URI.” There are many significant pitfalls on the road to highly configurable software, but perhaps the worst are the following.

- It frequently leads to analysis paralysis, in which the problem seems so big and so intractable that the team spends all of their time thinking about how to solve it and none of their time actually solving anything.
- The system becomes so complex to configure that many of the benefits of its flexibility are lost, to the extent where the effort involved in its configuration is comparable to the cost of custom development.

### Types of Configuration

Configuration information can be injected into your application at several points in your build, deploy, test, and release process, and it’s usual for it to be included at more than one point.

- Your build scripts can pull configuration in and incorporate it into your binaries at **build time**.
- Your packaging software can inject configuration at **packaging time**, such as when creating assemblies, ears, or gems.
- Your deployment scripts or installers can fetch the necessary information or ask the user for it and pass it to your application at **deployment time **as part of the installation process.
- Your application itself can fetch configuration at **startup time **or run time.

Generally, **we consider it bad practice to inject configuration information at build or packaging time. **This follows from the principle that you should be able to deploy the same binaries to every environment so you can ensure that the thing that you release is the same thing that you tested. The corollary of this is that anything that changes between deployments needs to be captured as configuration, and not baked in when the application is compiled or packaged.

It is usually important to be able to configure your application at deployment time so that you can tell it where the services it depends upon (such as database, messaging servers, or external systems) belong. For example, if the runtime configuration of your application is stored in a database, you may want to pass the database’s connection parameters to the application at deployment time so it can retrieve it when it starts up.

Finally, you may need to configure your application at startup time or at run time. Startup-time configuration can be supplied either in the form of environment variables or as arguments to the command used to start the system. Alternatively, you can use the same mechanisms that you use for runtime configuration: registry settings, a database, configuration files, or an external configuration service (accessed via SOAP or a REST-style interface, for example).

Whatever mechanism you choose, we strongly recommend that, as far as practically possible, **you should try and supply all configuration information for all the applications and environments in your organization through the same mechanism.** This isn’t always possible, but when it is, it means that there is a single source of configuration to change, manage, version-control, and override (if necessary). In organizations where this practice isn’t followed, we have seen people regularly spend hours tracking down the source of some particular setting in one of their environments.

### Managing Application Configuration

Configuration information is often modeled as a set of name-value strings. Sometimes it is useful to use types in your configuration system and to organize it hierarchically. Windows properties files that contain name-value strings organized by headings, YAML files popular in the Ruby world, and Java properties files are relatively simple formats that provide enough flexibility in most cases. Probably the useful limit of complexity is to store configuration as an XML file.

There are a few obvious choices for where to store your application configuration: a database, a version control system, or a directory or registry. **Version control is probably the easiest—you can just check in your configuration file, and you get the history of your configuration over time for free.** It is worth keeping a list of the available configuration options for your application in the same repository as its source code.

It is often important to keep the actual configuration information specific to each of your application’s testing and production environments in a repository separate from your source code. This information generally changes at a different rate to other version-controlled artifacts. However, if you take this route, you will have to be careful to track which versions of configuration information match with which versions of the application. This separation is particularly relevant for security-related configuration elements, such as passwords and digital certificates, to which access should be restricted. **Don’t Check Passwords into Source Control or Hard-Code Them in Your Application.** Passwords should always be entered by the user performing the deployment. There are several acceptable ways to handle authentication for a multilayer system. You could use certificates, a directory service, or a single sign-on system

Databases, directories, and registries are convenient places to store configuration since they can be accessed remotely. However, **make sure to keep the history of changes to configuration for the purposes of audit and rollback**. Either have a system that automatically takes care of this, or treat version control as your system of reference for configuration and have a script that loads the appropriate version into your database or directory on demand.

The most efficient way to manage configuration is to have a **central service **through which every application can get the configuration it needs. This is as true for packaged software as it is for internal corporate applications and software as a service hosted on the Internet. The main difference between these scenarios is in when you inject the configuration information—at packaging time for packaged software, or at deploy time or run time otherwise.

Probably the easiest way for an application to access its configuration is via the filesystem. This has the advantage of being cross-platform and supported in every language—although it may not be suitable for sand-boxed runtimes such as applets. There is also the problem of keeping configuration on filesystems in sync if, for example, you need to run your application on a cluster.

Another alternative is to fetch configuration from a **centralized repository **such as a RDBMS, LDAP, or a web service. Applications can perform an HTTP GET which includes the application and environment name in the URI to fetch their configuration. This mechanism makes most sense when configuring your application at deployment time or run time. You pass the environment name to your deployment scripts (via a property, command-line switch, or environment variable), and then your scripts fetch the appropriate configuration from the configuration service and make it available to the application, perhaps as a file on the filesystem.

### Modeling Configuration

Each configuration setting can be modeled as a tuple, so the configuration for an application consists of a set of tuples. However, the set of the tuples available and their values typically depend on three things:

- The application
- The version of the application
- The environment it runs in (for example, development, UAT, performance, staging, or production)

Whatever you use to represent and serve configuration information—XML files in source control or a RESTful web service—should be able to handle these various dimensions. Here are some use cases to consider when modeling configuration information.

- Adding a new environment (a new developer workstation perhaps, or a capacity testing environment). In this case you’d need to be able to specify a new set of values for applications deployed into this new environment.
- Creating a new version of the application. Often, this will introduce new configuration settings and get rid of some old ones. You should ensure that when you deploy this new version to production, it can get its new settings, but if you have to roll back to an older version it will use the old ones.
- Promoting a new version of your application from one environment to another. You should ensure that any new settings are available in the new environment, but that the appropriate values are set for this new environment.
- Relocating a database server. You should be able to update, very simply, every configuration setting that references this database to make it point to the new one.
- Managing environments using virtualization. You should be able to use your virtualization management tool to create a new instance of a particular environment that has all the VMs configured correctly. You may want to include this information as part of the configuration settings for the particular version of the application deployed into that environment.

One approach to managing configuration across environments is to make the expected production configuration the default and to override this default in other environments as appropriate (ensure you have firewalls in place so that production systems don’t get hit by mistake). This means that any environment-specific tailoring is reduced to only those configuration properties that must be changed for the software to work in that particular environment. This simplifies the picture of what needs to be configured where. However, it also depends on whether or not your application’s production configuration is privileged—some organizations expect the production configuration to be kept in a separate repository from that of other environments.

In the same way that your application and build scripts need testing, so do your configuration settings. There are two parts to testing configuration. The first stage is to ensure that references to external services in your configuration settings are good. You should, as part of your deployment script, ensure that the messaging bus you are configured to use is actually up and running at the address configured, and that the mock order fulfillment service your application expects to use in the functional testing environment is working. At the very least, you could ping all external services. Your deployment or installation script should fail if anything your application depends on is not available—this acts as a great smoke test for your configuration settings.

The second stage is to actually run some smoke tests once your application is installed to make sure it is operating as expected. This should involve just a few tests exercising functionality that depends on the configuration settings being correct. Ideally, these tests should stop the application and fail the installation or deployment process if the results are not as expected.

### Managing Configuration Across Applications

The problem of managing configuration is particularly complex in medium and large organizations where many applications have to be managed together. Usually in such organizations, legacy applications exist with esoteric configuration options that are poorly understood. One of the most important tasks is to keep a catalogue of all the configuration options that each of your applications has, where they are stored, what their lifecycle is, and how they can be changed. If possible, such information should be generated automatically from each application’s code as part of the build process. But where this is not possible, it should be collected in a wiki or other document management system.

It is important to know **what the current configuration of each running application is**. The goal is to be able to see each application’s configuration through your operation team’s production monitoring system, which should also display which version of each application is deployed in each environment. Your configuration information should always be applied through this process.

Treat your application’s configuration the same way you treat your code. Manage it properly, and test it. Here is a list of principles to consider when creating an application configuration system:

- Consider where in your application’s lifecycle it makes sense to inject a particular piece of configuration—at the point of assembly where you are packaging your release candidate, at deployment or installation time, at startup time, or at run time. **Speak to the operations and support team to work out what their needs are**.
- **Keep the available configuration options for your application in the same repository as its source code, but keep the values somewhere else**. Configuration settings have a lifecycle completely different from that of code, while passwords and other sensitive information should not be checked in to version control at all.
- Configuration should always be performed by automated processes using values taken from your configuration repository, so that you can always identify the configuration of every application in every environment.
- Your configuration system should be able to provide different values to your application (including its packaging, installation, and deployment scripts) based on the application, its version, and the environment it is being deployed into. It should be easy for anyone to see what configuration options are available for a particular version of an application across all environments it will be deployed into.
- Use clear naming conventions for your configuration options. Avoid obscure or cryptic names. Try to imagine someone reading the configuration file without a manual—it should be possible to understand what the configuration properties are.
- Ensure that your configuration information is modular and encapsulated so that changes in one place don’t have knock-on effects for other, apparently unrelated, pieces of configuration.
- Use the DRY (don’t repeat yourself) principle. Define the elements of your configuration so that each concept has only one representation in the set of configuration information.
- Be minimalist: Keep the configuration information as simple and as focused as possible. Avoid creating configuration options except where there is a requirement or where it makes sense to do so.
- Avoid overengineering the configuration system. Keep it as simple as you can. Ensure that you have tests for your configuration that are run at deployment or installation time. Check that the services your application depends upon are available, and use smoke tests to assert that any functionality depending on your configuration settings works as it should.