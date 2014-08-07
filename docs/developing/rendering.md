Once a .php script within `site-root` begins handling a request, it can output whatever headers and content it wants.

Most responses will be rendered by the RequestHandler::respond method though. The static property `RequestHandler::$responseMode` determines what format a response will be
rendered in, and defaults to `'html'`. Scripts or extending classes may switch $responseMode to based on their own logic. 

When `RequestHandler::respond` is invoked, it takes two parameters: `$responseId` and `$responseData`. Regardless of response mode, `$responseId` will always be provided
to the client via the `X-Response-ID` response header.

## Response Modes

### json
This is the simplest response mode, `$responseData` is encoded to JSON and outputted with `Content-Type: application/json`

### html
The default response mode, a dwoo template will be searched for in `html-templates` combining the `$responseId` with the request path from right-to-left. Output from the
template will be returned to the client with `Content-Type: text/html`

To continue the example from the [routing page](/docs/developing/routing), if the request handler at `http://codeforphilly.org/projects/Code_for_Philadelphia/edit`
invoked `RequestHandler::respond('projectEdit', ['success' => true, 'data' => $Project])`, the site would search in this order to find the best template:

1. html-templates/projects/Code_for_Philadelphia/edit/projectEdit.tpl
2. html-templates/projects/Code_for_Philadelphia/projectEdit.tpl
3. html-templates/projects/projectEdit.tpl
4. html-templates/projectEdit.tpl

In the code for codeforphilly.org, #3 will match first and be used to render the responseData to HTML, with each key in the responseData array becoming a variable available from dwoo.

### xml
Handled the same way as html, but template output will be returned to the client with `Content-Type: text/xml`.

### return
In `'return'` mode, an array containing `responseID` and `data` will be returned from the respond() fuction to the caller.

### pdf
A template is found following the same logic as for the `'html'` response mode, but responseId is suffixed with `.pdf` first and the output from the template is written to /tmp and
processed with wkhtmltopdf to be returned with `Content-Type: application/pdf`. For the example request described above, a template named `projectEdit.pdf.tpl` would need to be found.

### csv
The `CSV::fromRecords` method is used to attempt to render `$responseData` in CSV format with `Content-Type: text/csv` and `Content-Disposition: attachment; filename=$responseId.csv`

## Emergence Development Roadmap

- There should be a way for classes extending RequestHandler to declare a list of allowed response modes which will be automatically selected from based on the  `Accept` requset header.