# Aerisweather forecast formatting script - multilingual, international

This script was designed to replace the _DS-forecast.php_ script as **DarkSky has discontinued offering their API keys effective 31-Mar-2020** as DarkSky was purchased by Apple and announced the end of their API for existing users the end of 2021.  

This script is the third provider to be used for worldwide forecasts, and I hope Aerisweather will continue to offer the API. It is free for weather stations that upload to PWSweather.com and for-fee to others.  

Aerisweather DOES NOT currently provide international versions of the English forecast text -- it is is only one short sentence and is used as the icon description.  
The _AW-forecast-lang.php_ provides translation capabilities for Saratoga Template languages for additional text added to the text forecast from data provided by Aerisweather API (i.e. Temperature High/Low, Probibility of precipation, Wind direction, speed and gust, UV index ). The _AW-forecast-lang.php_ which has the encoded lookups for boilerplate language translations is provided as a separate script for easy update as new languages are added.  

In addition, the _AW-forecast.php_ uses the translation feature from the [WXSIM plaintext-parser](https://github.com/ktrue/WXSIM-forecast) so you can add missing English forecast text translations easily to the associated language file.  
Be sure to read/heed the _./AWlang/README-before-updating-these-files.txt_ file before updating the _plaintext-parser-lang-**LL**\-txt_ files to maintain the proper ISO-8859-n character set in the file. Otherwise, the translation may fail with odd characters in the forecast text. Yes, you can use a copy of your existing updated _plaintext-parser-lang-**LL**\-txt_ if you have the _plaintext-parser.php_ script installed. Just copy it to the _./AWlang/_ directory and do updates there for the _AW-forecast.php_ use.  

Currently, _AW-forecast.php_ supports the following languages (in addition to English): **af,bg,cs,ct,de,dk,el,es,fi,fr,he,hu,it,nl,no,pl,pt,ro,se,si,sk,sr**  

## Sample Output

<img sce="./sample-output.png" alt="sample output">


In order to use this script you need to:

1.  Register for and acquire a free Aerisweather API key IF your weather station is uploading to PWSweather.com.
    1.  Browse to **[https://www.aerisweather.com/signup/pws/](https://www.aerisweather.com/signup/pws/)** and associate your pwsweather.com User ID to get your Access ID and Secret Key.
    2.  insert the Access ID in **$AWAPIkey** in the AW-forecast.php script or as **$SITE\['AWAPIkey'\]** in _Settings.php_ for Saratoga template users.  
        and insert the Secret Key in **$AWAPIsecret** in the AW-forecast.php script or as **$SITE\['AWAPIsecret'\]** in _Settings.php_ for Saratoga template users.
    3.  Customize the **$AWforecasts** array (or **$SITE\['AWforecasts'\]** in _Settings.php_) with the location names, latitude/longitude for your forecasts. The first entry will be the default one used for forecasts.
    4.  Select the default units for display - 4 different unit selections are available (see below).
2.  Use this script ONLY on your personal, non-commercial weather station website.
3.  Leave attribution (and hotlink) to Aerisweather as the source of the data in the output of the script.

Adhere to these three requirements, and you should have fair use of this data from Aerisweather.

## Settings in the AW-forecast.php script

```
// Settings ---------------------------------------------------------------
// REQUIRED: api.aerisapi.com API Access ID and Secret keys.
// If you are uploading to pwsweather.com, you can get a free key at https://www.aerisweather.com/signup/pws/
$AWAPIkey = 'specify-for-standalone-use-here';    // Aeris Access ID; use this only for standalone / non-template use
$AWAPIsecret = 'specify-for-standalone-use-here'; // Aeris Secret Key; use this only for standalone / non-template use

// NOTE: if using the Saratoga template, add to Settings.php a line with:
//    $SITE\['AWAPIkey'\] = 'your-api-client-key-here';
//    $SITE\['AWAPIsecret'\] = 'your-api-secret-key-here';
// and that will enable the script to operate correctly in your template
//
$iconDir ='./forecast/images/';	// directory for carterlake icons './forecast/images/'
$iconType = '.jpg';				// default type='.jpg'
//                        use '.gif' for animated icons fromhttp://www.meteotreviglio.com/
//
// The forecast(s) .. make sure the first entry is the default forecast location.
// The contents will be replaced by $SITE\['AWforecasts'\] if specified in your Settings.php

$AWforecasts = array(
 // Location|lat,long  (separated by | characters)
'Saratoga, CA, USA|37.27465,-122.02295',
'Auckland, NZ|-36.910,174.771', // Awhitu, Waiuku New Zealand
'Assen, NL|53.02277,6.59037',
'Blankenburg, DE|51.8089941,10.9080649',
'Cheyenne, WY, USA|41.144259,-104.83497',
'Carcassonne, FR|43.2077801,2.2790407',
'Braniewo, PL|54.3793635,19.7853585',
'Omaha, NE, USA|41.19043,-96.13114',
'Johanngeorgenstadt, DE|50.439339,12.706085',
'Athens, GR|37.97830,23.715363',
'Haifa, IL|32.7996029,34.9467358',
'Greenville, ME|45.4608,-69.5904',
'Tehachapi, CA|35.1322,-118.4491',
);

//
$maxWidth = '640px';                      // max width of tables (could be '100%')
$maxIcons = 10;                           // max number of icons to display
$maxForecasts = 14;                       // max number of Text forecast periods to display
$maxForecastLegendWords = 4;              // more words in forecast legend than this number will use our forecast words
$numIconsInFoldedRow = 10;                 // if words cause overflow of $maxWidth pixels, then put this num of icons in rows
$autoSetTemplate = true;                  // =true set icons based on wide/narrow template design
$cacheFileDir = './';                     // default cache file directory
$cacheName = "AW-forecast-json.txt";      // locally cached page from AW
$refetchSeconds = 3600;                   // cache lifetime (3600sec = 60 minutes)
//
// Units:
// si: SI units (C,m/s,hPa,mm,km)
// ca: same as si, except that windSpeed and windGust are in kilometers per hour
// uk2: same as si, except that nearestStormDistance and visibility are in miles, and windSpeed and windGust in miles per hour
// us: Imperial units (F,mph,inHg,in,miles)
//
$showUnitsAs  = 'ca'; // ='us' for imperial, , ='si' for metric, ='ca' for canada, ='uk2' for UK
//
$charsetOutput = 'ISO-8859-1';        // default character encoding of output
//$charsetOutput = 'UTF-8';            // for standalone use if desired
$lang = 'en';	// default language
$foldIconRow = false;  // =true to display icons in rows of 5 if long texts are found
$timeFormat = 'Y-m-d H:i T';  // default time display format

$showConditions = true; // set to true to show current conditions box

// ---- end of settings ---------------------------------------------------
```
For Saratoga template users, you normally do not have to customize the script itself as the most common configurable settings are maintained in your _Settings.php_ file. This allows you to just replace the _AW-forecast.php_ on your site when new versions are released.  
You DO have to add a **$SITE\['AWAPIkey'\] = '_your-key-here_';** and a **$SITE\['AWforecasts\] = array( ...);** entries to your _Settings.php_ file to support this and future releases of the script.

**$AWAPIkey = 'specify-for-standalone-use-here';**

This setting is for **standalone** use (do not change this for Saratoga templates).  
Register for a Aerisweather **Access ID** and **Secret Key** at **[https://www.aerisweather.com/signup/pws/](https://www.aerisweather.com/signup/pws/)** and replace _specify-for-standalone-use-here_ with the registered **API Access ID**. The script will nag you if this has not been done.  

**For Saratoga template users**, do the registration at the Aerisweather API site above, then put your Access ID in your _Settings.php_ as:  

$SITE\['AWAPIkey'\] = '_your-key-here_';  

to allow easy future updates of the AW-forecast.php script by simple replacement.

**$AWAPIsecret = 'specify-for-standalone-use-here';**

This setting is for **standalone** use (do not change this for Saratoga templates).  
Register for a Aerisweather Access ID at **[https://www.aerisweather.com/signup/pws/](https://www.aerisweather.com/signup/pws/)** and replace _specify-for-standalone-use-here_ with the registered **Secret Key**. The script will nag you if this has not been done.  

**For Saratoga template users**, do the registration at the Aerisweather API site above, then put your **Secret Key** in your _Settings.php_ as:  

**$SITE\['AWAPIsecret'\] = '_your-key-here_';**  

to allow easy future updates of the AW-forecast.php script by simple replacement.

**$iconDir**

This setting controls whether to display the NOAA-styled icons on the forecast display.  
Set $iconDir to the relative file path to the Saratoga Icon set (same set as used with the WXSIM plaintext-parser.php script).  
Be sure to include the trailing slash in the directory specification as shown in the example above.  
**Saratoga template users:** Use the _Settings.php_ entry for **$SITE\['fcsticonsdir'\]** to specify this value.

**$iconType**

This setting controls the extension (type) for the icon to be displayed.  
**\='.jpg';** for the default Saratoga JPG icon set.  
**\='.gif';** for the Meteotriviglio animated GIF icon set.  
**Saratoga template users:** Use the _Settings.php_ entry for **$SITE\['fcsticonstype'\]** to specify this value.

**$AWforecasts = array(  
// Location|forecast-URL (separated by | characters)  
'Saratoga, CA, USA|37.27465,-122.02295',  
'Auckland, NZ|-36.910,174.771', // Awhitu, Waiuku New Zealand  

...  
);**

This setting is the primary method of specifying the locations for forecasts. It allows the viewer to choose between forecasts for different areas based on a drop-down list box selection.  
Each entry has the format '<location name>|<latitude>,<longitude>'. Note that latitude and longitude must use decimal-period format as shown, and positive numbers omit the '+' sign. For latitude, positive is North/negative is South of the Equator. For longitude, positive is East of GMT/negative is West of GMT.  
**Saratoga template users**: Use the _Settings.php_ entry for **$SITE\['AWforecasts'\] = array(...);** to specify the list of sites and URLs.

**$maxWidth**

This variable controls the maximum width of the tables for the icons and text display. It may be in pixels (as shown), or '100%'. The Saratoga/NOAA icons are 55px wide and there are up to 10 icons, so beware setting this width too small as the display may be quite strange.

**$maxIcons**

This variable specifies the maximum number of icons to display in the graphical part of the forecast. Some forecast locations may have up to 10 days of forecast (10 icons) so be careful how wide the forecast may become on the page.

**$cacheFileDir**

This setting specifies the directory to store the cache files. The default is the same directory in which the script is located.  
Include the trailing slash in the directory specification.  
**Saratoga template users:** Use the _Settings.php_ entry for **$SITE\['cacheFileDir'\]** to specify this value.

**$cacheName**

This variable specifies the name of the cache file for the AW forecast page.

**$refetchSeconds**

This variable specifies the cache lifetime, or how long to use the cache before reloading a copy from Aerisweather. The default is 3600 seconds (60 minutes). Forecasts don't change very often, so please don't reduce it below 60 minutes to minimize your API access count and keep it to the free API usage range.

**$showUnitsAs**

This setting controls the units of measure for the forecasts.  
**\='si'** SI units (C,m/s,hPa,mm,km)  
**\='ca'** same as si, except that windSpeed and windGust are in kilometers per hour  
**\='uk2'** same as si, except that nearestStormDistance and visibility are in miles, and windSpeed and windGust in miles per hour  
**\='us'** Imperial units (F,mph,inHg,in,miles)  
**Saratoga template users:** This setting will be overridden by the **$SITE\['AWshowUnitsAs'\]** specified in your _Settings.php_.  

**$foldIconRow**

This setting controls 'folding' of the icons into two rows if the aggregate width of characters exceeds the $maxSize dimension in pixels.  
**\= true;** is the default (fold the row)  
**\= false;** to select not to fold the row.  
**Saratoga template users:** Use the _Settings.php_ entry for **$SITE\['foldIconRow'\]** to specify this value.

More documentation is contained in the script itself about variable names/arrays made available, and the contents. The samples below serve to illustrate some of the possible usages on your weather website. To customize translations, the appropriate language file in **_./AWlang/_** directory needs to be changed. See the caution in the README file in the directory before doing changes to those files.

## Sample usage

```PHP
<?php  
$doIncludeAW = true;  
include_once("AW-forecast.php"); ?>
```
You can also include it 'silently' and print just a few (or all) the contents where you'd like it on the page
```PHP
<?php  
$doPrintAW = false;  
include_once("AW-forecast.php"); ?>  
```
then on your page, the following code would display just the current and next time period forecast:
```PHP
<table>
<tr align="center" valign="top">
<?php print "<td>$AWforecasticons[0]</td><td>$AWforecasticons[1]</td>\n"; ?>
</tr>
<tr align="center" valign="top">
<?php print "<td>$AWforecasttemp[0]</td><td>$AWforecasttemp[1]</td>\n"; ?>
</tr>
</table>
```
Or if you'd like to include the immediate forecast with text for the next two cycles:
```PHP
<table>
<tr valign="top">
<?php print "<td align=\"center\">$AWforecasticons[0]<br />$AWforecasttemp[0]</td>\n"; ?>
<?php print "<td align=\"left\" valign=\"middle\">$AWforecasttext[0]</td>\n"; ?>
</tr>
<tr valign="top">
<?php print "<td align=\"center\">$AWforecasticons[1]<br />$AWforecasttemp[1]</td>\n"; ?>
<?php print "<td align=\"left\" valign=\"middle\">$AWforecasttext[1]</td>\n"; ?>
</tr>
</table>
```
If you'd like to style the output, you can easily do so by setting a CSS for class **AWforecast** either in your CSS file or on the page including the AW-forecast.php (in include mode):

```CSS
<style type="text/css">    
.AWforecast {    
    font-family: Verdana, Arial, Helvetica, sans-serif;    
    font-size: 9pt;    
}    
</style>
```

**Icon Set** is available at https://saratoga-weather.org/saratoga-icons2.zip
