
# **DEPRECATED, NO LONGER USED**

# Download Only Support

### What is it?


The ability to schedule a download of packages apart from the installation.  This allows customers to preload client systems before an outage and then schedule updates during that outage.

### The Plan



We cannot use the yum-downloadonly plugin because it is a "Interactive" plugin.  It is interactive because it terminates the session.  The way we use yum means that we can't really load any interactive plugins without loading them all.

This means we'll need to duplicate it's functionality just for our plugin (basically merging that functionality into yum-rhn-plugin.  This isn't a big deal because it is really only a couple lines of code.
### Whats Needed




    Add client side support (client utils and yum-rhn-plugin)
    Add backend support
    Add schema support
    Add Package UI support  (SDC & SSM)
    Add Errata UI support (SDC & SSM)
#### yum-rhn-plugin



basically add:

    def postdownload_hook(conduit):
        opts, commands = conduit.getCmdLine()
        # Don't die on errors, or we'll never see them.
        if not conduit.getErrors() and opts.dlonly:
            raise PluginYumExit('exiting because --downloadonly specified ')
to the plugin (From downloadonly)

Then add something to set the dlonly option within packages.py.
#### Schema changes

Add new action type.  Should be able to re-use rhnActionPackage for the package information.  

#### Backend



Support new action type
