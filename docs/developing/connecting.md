## Via your web browser
Navigate to <kbd>http://yourhost.name/develop</kbd> for any Emergence site hostname, and then log in with a developer-level account when
prompted. Opening this path in your browser will load the web-based IDE for Emergence.

Source code for the IDE is available under `sencha-workspace/EmergenceEditor`.

## Via remote client application
The `/develop` endpoint implements the WebDAV protocol for file listing and management. You can connected to it with any WebDAV-capable
client using your hostname and a developer-level username and password. Be sure that you are specifying `/develop` somewhere in your
connection options (usually called "working directory" or "initial directory"), as no route on your site outside `/develop` will respond
to WebDAV requests.