# Question
I want to download a csv file that you can download from the following public page:
```
https://www.historique-meteo.net/france/rh-ne-alpes/annecy-haute-savoie/2016/02/
```

On the top right you see a download button "Export CSV" which will download some csv file. Looking at the code with the developer console you will find the html code for this button to be

```html
<a href="/site/export.php?ville_id=1025&amp;annee=2016&amp;mois=02" class="btn btn-small btn-theme pull-right">export CSV</a>
```

So I tried to download the csv file using curl/wget by trying the following commands

```bash
curl "https://www.historique-meteo.net/site/export.php?ville_id=1025&annee=2016&mois=02"
wget "https://www.historique-meteo.net/site/export.php?ville_id=1025&annee=2016&mois=02"
```

but I did not get the csv file. I either get some html data or a 503 error.

How to do it correctly?

# Answer
Go to the page in your browser, click `Export CSV`, get past the Cloudflare DDOS protection screen, cancel download, and go back to the page. Then open the network monitor tab (<kbd>Ctrl+Shift+E</kbd> in Firefox), and click `Export CSV` again. You should see one GET request like this

[![https://imgur.com/H8oM13t.png](https://imgur.com/H8oM13t.png)](https://imgur.com/H8oM13t.png)

Then right click it > copy > copy as curl. Which should give you a request which looks something like this

```bash
curl 'https://www.historique-meteo.net/site/export.php?ville_id=1025&annee=2016&mois=02' -H 'User-Agent: YourUA' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8' -H 'Accept-Language: en-US,en;q=0.5' --compressed -H 'Connection: keep-alive' -H 'Referer: https://www.historique-meteo.net/france/rh-ne-alpes/annecy-haute-savoie/2016/02/' -H 'Cookie: __cfduid=UID; cf_chl_2=CHL; cf_chl_prog=x19; cf_clearance=CLR' -H 'Upgrade-Insecure-Requests: 1' -H 'Pragma: no-cache' -H 'Cache-Control: no-cache'
```

You can then use this to download the CSV file from your terminal for however long they keep the cloudflare cookie stored. Use `-o output.csv` to save the file. This isn't a cloudflare bypass, and any suspicious activities will likely make the cookie expire again, but another thing you could do is use [selenium][1]

[1]: https://github.com/SeleniumHQ/selenium

# Original Link
https://stackoverflow.com/questions/66622729/how-to-use-wget-or-curl-to-download-a-php-generated-csv-file-from-a-webpage/66626183#66626183
