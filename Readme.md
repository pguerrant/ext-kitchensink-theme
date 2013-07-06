### Ext JS 4.2.1 KitchenSink For Testing Custom Themes

When creating a custom theme it is often desirable to test the theme against a wide
variety of Ext JS Components.  Testing a theme against the Ext JS KitchenSink
goes a long way towards ensuring your app is compatible with the universe of
Ext JS components, whereas testing a theme against your app, may not ensure
the theme is compatible with other apps, if there are components your application
does not use.

This problem appears simple to solve.  Just copy the Ext JS KitchenSink example
into your workspace, right?  Not so fast - this doesn't work as easily as one
would expect.  Because the KitchenSink example is an application that lives
within the Ext JS SDK, it makes certain assumptions about the relative paths
of its dependencies that are not true when running an application from your
workspace generated using Sencha Cmd.  This KitchenSink example fixes
all those issues so that you can run the KitchenSink from your Sencha Cmd
4.0 workspace and use it to test your custom theme.

You will need Sencha Cmd 4.0 or higher to run this example.  The example
was upgraded to use Sencha Cmd 4.0 so that you can use "sencha app watch"
when developing your theme.  It makes development a lot faster.

To use this example just download the zip and unzip it to a "kitchensink"
directory in your workspace.  Then open "kitchensink/.sencha/app/sencha.cfg"
and modify the app.theme property as follows:

    app.theme=my-custom-theme

###What Exactly Had to Change?
In case you're interested in the details of what exactly was changed to
get the Ext JS 4.2.1 KitchenSink to run in your Sencha Cmd generated workspace,
here it is:

1. remove shared/examples.js from index.html
2. remove last line from sass/config.rb

        require File.join(cur_dir, '../../../..', 'packages', 'ext-theme-base', 'sass', 'utils.rb')
This is a relic from days past when Sencha Cmd didn't automatically handle inclusion of SASS utils.
3. in sass/example/theme.html replace `../../../../../packages/` with `../../../ext/packages/` for manifest/shortcuts includes.
4. in sass/example/theme.html replace `../../../../../extjs/ext-dev.js` with `../../../ext/ext-dev.js`
5. in index.html replace:

        <script src="../../examples/shared/include-ext.js?nocss"></script>
        <script src="../../examples/shared/options-toolbar.js"></script>
with:

        <link rel="stylesheet" href="bootstrap.css">
        <script src="../ext/ext-dev.js"></script>
6. from the kitchensink dir run

        sencha config -prop app.cmd.version=1 then app upgrade
7. remove the following lines from sass/example/theme.html

        <<<<<<< Generated
            <script type="text/javascript" src="../../../ext/packages/ext-theme-base/sass/example/manifest.js"></script>
            <script type="text/javascript" src="../../../ext/packages/ext-theme-base/sass/example/shortcuts.js"></script>
        =======
and:

        >>>>>>> Custom
8. add the following to Main.js onNavSelectionChange()

        else {
            clsProto.themeInfo = Ext.applyIf(clsProto.themeInfo || {}, clsProto.themes.neptune);
        }
this assumes that your theme extends neptune.  If your theme extends classic change "neptune" to "classic".
9. In DescriptionPanel.js and CodePreview.js replace this:

        this.ui = (Ext.themeName === 'neptune') ? 'light' : 'default';
with this:

        this.ui = 'light';
once again this assumes a neptune-derived theme.  If your theme extends classic skip this step.
10. Run the following command from the kitchensink directory:

        sencha app refresh
