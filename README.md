# jazz-plugin-maven-archetype
Maven archetype to generate a jazz plugin project. If you want to get your feet wet as quickly as possible, just read [Bootstrapping](#Bootstrapping-a-new-jazz-service-using-this-archetype)

All of the following sections make assumptions about your working environment. At least git and maven are absolutely necessary to use this archetype. The instructions assume that both executables are on your path and consequently useable with just the program name.

Further, you will need to have installed the [jazz base service](URL), which is a dependency for any services created with this archetype. Any maven builds without this dependency available in your local maven repository will fail. Also, you need to have the [jazz sdk p2 repository](URL) available in your maven repository. It is also suggested that you have the [jazz jetty environment](https://github.com/innerjoin/jazz-debug-environment) available for a rapid development and test cycle.

## Example archetype usage

This section shows how to create an example service from the archetype. It uses default settings and is absolutely not recommended for usage in a productive environment and only serves as an example.

1. Clone this repository.
2. Run setup.ps1 / setup.sh
3. Three consecutive successful maven build cycles should run.
4. Inside the target folder, there should now be a folder named `com.siemens.example.parent` with the example service structure.
5. You can now run `mvn package` from inside the `com.siemens.example.parent` folder to build the example service plugin.

## Bootstrapping a new jazz service using this archetype

Using paramters when running the automated setup, a new service can be created with proper package declaration, groupId and names already set. Passing the right parameters will allow you to get coding right away. In this example, I will demonstrate how to create a service with the groupId `org.company.example`, a version of `0.0.1-SNAPSHOT` and a service named `MyService`.

TODO: add bash example

1. Clone this repository.
2. Run setup.ps1, but this time passing values to all parameters (chose your own values):
    .\setup.ps1 -group org.company.example -version 0.0.1-SNAPSHOT -serviceName MyService
3. As above, this will place a plugin folder structure called `org.company.example.parent` in the target folder.
4. Copy this folder to wherever you want to work on your plugin.
5. Run `mvn package` to build the plugin files required to run the plugin as a service from this location.

### For jetty

When using jetty, the `plugin/` and `plugin/target` folders should be used for run-time dynamic lookup. The files edited here should be located in your `debug-environment/conf/jetty/user_configs` folder.

6. Add a property value (or a new file dedicated to just this property) which states which folders your plugin files are in. For the example generated here, it would look like this:
    org.company.example=target/dependency,target/classes
7. Add the location of your **plugin** folder to your list of workspaces. It is essential that you reference the plugin folder containing your plugin.xml here. (Windows folder location used as an example).
    D\:/workspaces/org.company.example.parent/plugin@start
8. Run the generate_jetty_config script.
9. Run jetty
10. After jetty has started, you can verify that your service is running by opening the services page, for example `https://localhost:7443/jazz/service/` and locating your service. You can also call the service directly, for example `https://localhost:7443/jazz/service/org.company.example.IMyService/helloWorld`, which will show a website saying `Hello World!!!`

## Detailed archetype usage

Using the archetype directly without the simple setup wrapper scripts is not recommended and you should only use this if you have a very specific use case. You will have to consider some details without which your resulting project will fail to build.

TODO: Add explanation.

## File structure explanation (using the simple example)

For brevity, this section only covers files and folders that are relevant to understanding the basic structure generated by the archetype (not the file structure of the actual archetype). A finished build will contain more files. files not mentioned here will generally not need to be touched when working on your plugin.

|File structure                                                                      | Explanation
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------
|<pre> com.siemens.example.parent                                              </pre>| 
|<pre> │   pom.xml                                                             </pre>| Parent pom of the plugin build. This is what you want to run most of the time to get a complete plugin build.
|<pre> ├───.mvn                                                                </pre>| Maven settings folder
|<pre> ├───feature                                                             </pre>| Feature module folder
|<pre> │   │   feature.xml                                                     </pre>| Feature configuration with required plugins, feature description and plugin definition
|<pre> │   │   pom.xml                                                         </pre>| Sub module pom for creating the plugin feature files
|<pre> │   └───target                                                          </pre>| Generated feature files
|<pre> ├───plugin                                                              </pre>| Plugin module folder. This contains the source code for your plugin and is the most important sub module of the plugin project.
|<pre> │   │   plugin.xml                                                      </pre>| Plugin definition. This is the file used by the application server to load your plugin. Contains the extension point and components used by our plugin to attach to the jazz application. Must contain the proper names of of your service.
|<pre> │   │   pom.xml                                                         </pre>| Sub module pom for creating plugin files 
|<pre> │   ├───conf                                                            </pre>| Default jazz configuration files for use with an application server
|<pre> │   ├───META-INF                                                        </pre>| 
|<pre> │   │       MANIFEST.MF                                                 </pre>| OSGI configuration of your plugin. Contains required bundles and imported packages as well as the bundle classpath. This needs to mirror the settings in your pom and contain all dependencies your plugin builds with.
|<pre> │   ├───src                                                             </pre>| Actual plugin source code
|<pre> │   │   └───main                                                        </pre>| 
|<pre> │   │       └───java                                                    </pre>| 
|<pre> │   │           └───com                                                 </pre>| 
|<pre> │   │               └───siemens                                         </pre>| 
|<pre> │   │                   └───example                                     </pre>| 
|<pre> │   │                       │   ExampleService.java                     </pre>| The main service implementation. This file needs to be mentioned in the plugin.xml
|<pre> │   │                       │   IExampleService.java                    </pre>| The main service interface. This file needs to be mentioned in the plugin.xml
|<pre> │   │                       └───builder                                 </pre>| Builder implementations used for the example
|<pre> │   └───target                                                          </pre>| Generated plugin files. If you are using jetty, your service description will have to point to this location for proper loading.
|<pre>\|       ├───dependency                                                  </pre>| Dependency jars. If you are using jetty, you will also have to reference this folder.
|<pre> ├───target                                                              </pre>| Generated parent build files
|<pre> └───update-site                                                         </pre>| Update site sub module. This aggregates all build results of the entire project and creates deployable zip files for production use with tomcat/liberty/websphere.
|<pre>     │   pom.xml                                                         </pre>| Sub module pom for aggregating all modules as a deployable package.
|<pre>     ├───target                                                          </pre>| Aggregated build results
|<pre>     │   └───com.siemens.example.updatesite-1.0.0-SNAPSHOT.zip           </pre>| Deployable plugin. This can be extracted and used on your application server.
|<pre>     └───templates                                                       </pre>| Template xml files that are filtered during the build. These files are neccessary for generating the proper zip file structure for deploying the plugin on an application server.
|<pre>         │     site.xml                                                  </pre>| Site descriptor for the loaded feature
|<pre>         │     update-site.ini                                           </pre>| Update site description with file location and name of the feature to be loaded
|<pre>         └───  zip.xml                                                   </pre>| Zip file structure of the resulting plugin

