# Changelog

### 0.9.2 ###
------------
This is a bugfix point release that resolves [3 issues](https://github.com/stormpath/stormpath-sdk-java/issues?milestone=4&state=closed):

- [Issue 30](https://github.com/stormpath/stormpath-sdk-java/issues/30): Custom data updates are not cached when calling account/group save()
- [Issue 28](https://github.com/stormpath/stormpath-sdk-java/issues/28): ResourceException getMessage() should return a more descriptive message
- [Issue 31](https://github.com/stormpath/stormpath-sdk-java/issues/31): Provide more detailed ResourceException messages (duplicate of Issue #28).

### 0.9.1 ###
-------------
This is a bugfix point release that resolves [1 issue](https://github.com/stormpath/stormpath-sdk-java/issues?milestone=3):

- [Issue 25](https://github.com/stormpath/stormpath-sdk-java/issues/25): account.addGroup and group.addAccount do not work

### 0.9.0 ###
----------------
This is a [milestone](https://github.com/stormpath/stormpath-sdk-java/issues?milestone=2&page=1&state=closed) / new feature release.

#### Custom Data! ####

You now have the ability to create, update, delete up to 10 Megabytes of your own custom data per `Account` or `Group` stored within Stormpath. This allows you to completely remove user tables within your application if you desire.

Read the [Custom Data announcement](http://www.stormpath.com/blog/custom-user-data-stormpath-now-beta) and the [Custom Data REST API documentation](http://docs.stormpath.com/rest/product-guide/#custom-data) for more information and how to safely use Custom Data in your own applications.


##### Persistance by Reachabliity #####

Custom Data is a resource like any other - you can `save()` modifications and `delete()` it if you like.  But, because it it has a 1-to-1 correlation with an owning `Account` or `Group`, it is a little extra special: you can also save, update and delete Custom Data just by saving the owning account or group.

#### Create Accounts via an Application ####

As a convenience, you may now create `account` and `group` resources directly via an application, without first needing to obtain the intermediate directory or group where they will be persisted:


#### Create Groups via an Application ####

Similar to accounts, as a convenience, you may create `group` resources directly via an application without first needing to obtain the intermediate directory where the group will be persisted:

#### Create Cloud Directories ####

You may now create and delete 'Cloud' (natively hosted) directories in Stormpath with the Stormpath Java SDK.  LDAP and Active Directory 'Mirrored' directories must still be created in the Stormpath Admin Console UI.  

#### Manage Account Store Mappings ####

[Account Store Mappings](http://docs.stormpath.com/rest/product-guide/#account-store-mappings) are useful in more advanced usages of Stormpath, for example, if you have more than one directory (or group) to assign to an application to create a merged user base for the application.

The Java SDK now allows you to add, remove, re-order and generally manage an Application's account store mappings for these more advanced use cases.  

### 0.8.1 ###
------------------
This is a bugfix point release that resolves [1 issue](https://github.com/stormpath/stormpath-sdk-java/issues?milestone=1&page=1&state=closed):

- [Issue #12](https://github.com/stormpath/stormpath-sdk-java/issues/14) application.authenticateAccount fails if caching is enabled

### 0.8.0 ###
------------------
#### Bug Fixes ####

Collection Resource iteration previously only represented the first page in a collection.  Iteration now transparently iterates over the entire collection, automatically requesting new pages from the server as necessary. 

#### New Features and Improvements ####

##### Caching #####

The SDK now has full caching support, utilizing a CacheManager interface (that produces/manages Cache instances).  If enabled, this improves performance by reducing round-trips to the Stormpath API servers.

An out-of-the-box production-grade CacheManager implementation - complete with default and per-region TTL/TTI configuration - may be configured for single-JVM applications.  

Multi-JVM applications (an application deployed across multiple JVMs) would likely want to use a distributed/clustered coherent Cache product like Hazelcast, Ehcache+TerraCotta, Oracle Coherence, Gigaspaces, etc.  To leverage these caching products, you must implement the two interfaces (`CacheManager` and `Cache`) to delegate to your Caching provider API of choice. 

In both cases, the Stormpath Java SDK will store resource data in separate cache regions.  Each region is named after a resource interface for which it caches data, e.g. `"com.stormpath.sdk.account.Account"`, allowing for custom caching rules per resource type.  This gives you finer control of resource caching behavior based on your preferences/needs.

##### Query Support #####

Two new query mechanisms were introduced - you choose which you want to use based on your preference and/or JVM language.

1. [Fluent](http://en.wikipedia.org/wiki/Fluent_interface) and type-safe query DSL: If you're using a type-safe language, you will find this convenient, especially when using an IDE that auto-completes.  You'll find writing valid queries fast! 
2. Map-based query methods.  These are not type safe, but might be desirable for some developers, maybe those using dynamically typed languages.  The map key/value pairs are simply REST API query parameters and values.  For example, the same results of the above fluent query could be achieved as follows in Groovy:
    ```groovy
    application.getAccounts [surname: '*Smith*', givenName: 'John',
                             orderBy: 'surname desc', expand: 'groups(offset:10,limit:10)'
                             offset: 20, limit: 25]
    ```

0.6.0
-------------

#### New Features and Improvements ####

AbstractResource
- Added setProperty(String name, Object value, final boolean dirty) method.
- Refactored setProperty(name, value) to delegate to this new method.
- Refactored getResourceProperty entirely to support silent swapping links to materialized Resource instances.

AbstractResourceCollection
- Refactored getCurrentPage() implementation to silently swap materialized collection with existing property
  (Change starts on line 72 and goes to line 84).  This refactoring calls the new
  AbstractResource.setProperty(name, value, false) method.

DefaultDataStore

- Added toSimpleReference(String propName, Resource resource)
- Updated toMap method implementation to use new toSimpleReference call (new else if statement on line 186)

