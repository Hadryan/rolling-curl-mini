Rolling Curl Mini
===============

Rolling Curl Mini is a fork of [Rolling Curl] (http://code.google.com/p/rolling-curl/).
It allows to process multiple HTTP requests in parallel using cURL PHP library.

For more information read [this article] (http://savreen.com/mnogopotochnyj-sbor-dannyx-s-ispolzovaniem-cepochek-svyazannyx-curl-zaprosov-chast-1/) (in russian).

Basic Usage Sample
-------------
``` php
...
require "RollingCurlMini.inc.php";
...
$o_mc = new RollingCurlMini(10);
...
$o_mc->add($url, $postdata, $callback, $userdata, $options, $headers);
...
$o_mc->execute();
...
```

Callbacks
-------------
Any request may have an individual callback - function/method to be called as this request is completed.
Callback accepts 4 parameters and may look like the following one:

``` php
/**
 * @param string $content - content of request response
 * @param string $url - URL of requested resource
 * @param array $info - cURL handle info
 * @param mixed $userdata - user-defined data passed with add() method
 */
function request_callback($content, $url, $info, $userdata) {
}
```

License
-------------
* [Apache License 2.0] (http://www.apache.org/licenses/LICENSE-2.0)



Rolling Scraper Abstract
===============

Rolling Scraper Abstract is a multipurpose scraping (crawling) framework which uses facilities of multi-curl and RollingCurlMini class.
It is a base PHP class which implement common functionality of a multi-curl scraper.
Particular functionality should be implemented in derived classes.
Particular scraper class should extend RollingScraperAbstract class and implement (override) two mandatory methods: _initPages and _handlePage.

Basic Usage Sample
-------------
``` php
class MyScraper extends RollingScraperAbstract
{
    ...
    public function __construct() {
        ...
        $this->modConfig(array(
            'state_time_storage' => '...', // temporal section of state storage (file path)
            'state_data_storage' => '...', // data section of state storage (file path)
            'scrape_life' => 0, // expiration time (secs) of scraped data
            'run_timeout' => 30, // max. time (secs) to execute scraper script
            'run_pages_loops' => 20, // max. number of loops through pages
            'run_pages_buffer' => 500, // page requests buffer size
            'curl_threads' => 10, // number of multi-curl threads
            'curl_options' => array(...), // CURL options used in multi-curl requests
        ));
        parent::__construct();
    }

    /**
     * Initialize the starting list of page requests
     */
    protected function _initPages() {
        ...
        // add page request. $url - page URL
        $this->addPage($url);
        ...
    }

    /**
     * Process response of a page request
     * @param string $cont - page content
     * @param string $url - url of request
     * @param array $aInfo - CURL info data
     * @param int $index - # of page request
     * @param array $aData - custom request data (part of request data)
     * @return bool
     */
    protected function _handlePage($cont, $url, $aInfo, $index, $aData) {
        ...
    }
    ...
}

$scraper = new MyScraper();
$bool = $scraper->run();
list($time_start, $time_end, , $time_run_start, , $n_pages_total, $n_pages_passed) =
    $scraper->getStateProgress();
if ($time_end) {
    echo sprintf('Completed at %s', date('Y.m.d, H:i:s', $time_end));
}
else {
    if ($bool)
        echo sprintf('In progress: %d/%d pages', $n_pages_passed, $n_pages_total);
    else
        echo 'Cancelled since another script instance is still running';
}
```
=========================================================
RollingCurlMini usage example

    HTTP traffic:
    GET /title/tt0468569/ HTTP/1.1
    User-Agent: Mozilla/5.0 (Windows NT 6.1; rv:12.0a2) Gecko/20120203 Firefox/12.0a2
    Host: www.imdb.com
    Accept: */*
    Referer: http://www.imdb.com/title/tt0468569/

    HTTP/1.1 200 OK
    Date: Fri, 02 Jan 2015 10:09:47 GMT
    Server: Server
    Content-Type: text/html;charset=UTF-8
    Content-Language: en-US
    Vary: Accept-Encoding,User-Agent
    Set-Cookie: uu=BCYk3CzQKr9_GoxDq9k6GBy_ESIGPK5FRc2F8uLVGN9Ndv2oBotY-AtSysjmR3qVt8orVCn-2sOR%0D%0ANxawARiHZtnN3j2aUtWZMjQ3jOKyac5l-GB9zn-on2QtUUCWqmL5-mhTA0H0aOnkzkSvbnAN7c38%0D%0Aq-VfYyBlO21nk3IS4lU6-I7kTXSmDgQWpltSOwrQ_NFoFX6ylfxKGMF-NiNu0a69bSgYHtczTfjJ%0D%0ANanOIXJ6tX9LjfbwVLedlofZJ1Ar-lskr03OauevosiXa3dFcy_lHA%0D%0A; Domain=.imdb.com; Expires=Wed, 20-Jan-2083 13:23:55 GMT; Path=/
    Set-Cookie: session-id=784-4187274-5820882; Domain=.imdb.com; Expires=Wed, 20-Jan-2083 13:23:55 GMT; Path=/
    Set-Cookie: session-id-time=1577873387; Domain=.imdb.com; Expires=Wed, 20-Jan-2083 13:23:55 GMT; Path=/
    P3P: policyref="http://i.imdb.com/images/p3p.xml",CP="CAO DSP LAW CUR ADM IVAo IVDo CONo OTPo OUR DELi PUBi OTRi BUS PHY ONL UNI PUR FIN COM NAV INT DEM CNT STA HEA PRE LOC GOV OTC "
    Transfer-Encoding: chunked
    
    
    GET /title/tt0068646/ HTTP/1.1
    User-Agent: Mozilla/5.0 (Windows NT 6.1; rv:12.0a2) Gecko/20120203 Firefox/12.0a2
    Host: www.imdb.com
    Accept: */*
    Referer: http://www.imdb.com/title/tt0068646/
    
    HTTP/1.1 200 OK
    Date: Fri, 02 Jan 2015 10:09:47 GMT
    Server: Server
    Content-Type: text/html;charset=UTF-8
    Content-Language: en-US
    Vary: Accept-Encoding,User-Agent
    Set-Cookie: uu=BCYvaE68PNmbJBJIV-Sz2v8UPBqi6kwq_am2h2sdD4PYcsJwcEH5PTV8qYTOQtvNPccARMkQ-vdJ%0D%0ApN3nKrQ_XDCKkj6YgLHB8RzbD7qhp-K63g6NrdSOX835OV-rjegqGcB5uiV_4e93LSYU891M2xVh%0D%0A04mOlgS_RR-F83RDZc4ECgM3RJ192zx-6Ug2buvNPIN0Mk5XuVlSuB8S5snWJWZJW7seTdN2l56S%0D%0Au545KJlSaMZEb49p1RnQ9hUczhmLRj341znSeoYaLq0TfH4eQPAiEw%0D%0A; Domain=.imdb.com; Expires=Wed, 20-Jan-2083 13:23:54 GMT; Path=/
    Set-Cookie: session-id=275-4555266-6048628; Domain=.imdb.com; Expires=Wed, 20-Jan-2083 13:23:54 GMT; Path=/
    Set-Cookie: session-id-time=1577873387; Domain=.imdb.com; Expires=Wed, 20-Jan-2083 13:23:54 GMT; Path=/
    P3P: policyref="http://i.imdb.com/images/p3p.xml",CP="CAO DSP LAW CUR ADM IVAo IVDo CONo OTPo OUR DELi PUBi OTRi BUS PHY ONL UNI PUR FIN COM NAV INT DEM CNT STA HEA PRE LOC GOV OTC "
    Transfer-Encoding: chunked
    
    
    GET /title/tt0111161/ HTTP/1.1
    User-Agent: Mozilla/5.0 (Windows NT 6.1; rv:12.0a2) Gecko/20120203 Firefox/12.0a2
    Host: www.imdb.com
    Accept: */*
    Referer: http://www.imdb.com/title/tt0111161/
    
    HTTP/1.1 200 OK
    Date: Fri, 02 Jan 2015 10:09:47 GMT
    Server: Server
    Content-Type: text/html;charset=UTF-8
    Content-Language: en-US
    Vary: Accept-Encoding,User-Agent
    Set-Cookie: uu=BCYqU413ogo5lQUkcAXoVeJmE3_HPLCKcXGSLTpmq1gwp4NArPHumf1OA0nEjxtfpSgAhriWI8SZ%0D%0AxGuLGIFV0w6o9p5UcmqYeymKNLqcGR96VuBGWQx9uR9b-YXKiGRhckOB9l8-Gub2_nV4Cr1jEx5n%0D%0AEQW0gBslmTgXWO8P6QtiOoromkAVW2gSg2CKsFzmxEwaUnwfF0mPTk5S4hAyDGX6b3u4sKcUdY8b%0D%0AMwutzBnqLg5bhTtRH_N1iJvcowYOHvBsu66RasCUfsoCIbq0vsi5TA%0D%0A; Domain=.imdb.com; Expires=Wed, 20-Jan-2083 13:23:54 GMT; Path=/
    Set-Cookie: session-id=875-2962809-9988242; Domain=.imdb.com; Expires=Wed, 20-Jan-2083 13:23:54 GMT; Path=/
    Set-Cookie: session-id-time=1577873387; Domain=.imdb.com; Expires=Wed, 20-Jan-2083 13:23:54 GMT; Path=/
    P3P: policyref="http://i.imdb.com/images/p3p.xml",CP="CAO DSP LAW CUR ADM IVAo IVDo CONo OTPo OUR DELi PUBi OTRi BUS PHY ONL UNI PUR FIN COM NAV INT DEM CNT STA HEA PRE LOC GOV OTC "
    Transfer-Encoding: chunked
    
    
    GET /name/nm0634240/?ref_=tt_ov_dr HTTP/1.1
    User-Agent: Mozilla/5.0 (Windows NT 6.1; rv:12.0a2) Gecko/20120203 Firefox/12.0a2
    Host: www.imdb.com
    Accept: */*
    Referer: http://www.imdb.com/title/tt0468569/
    Cookie: session-id-time=1577873387; session-id=784-4187274-5820882; uu=BCYk3CzQKr9_GoxDq9k6GBy_ESIGPK5FRc2F8uLVGN9Ndv2oBotY-AtSysjmR3qVt8orVCn-2sOR%0D%0ANxawARiHZtnN3j2aUtWZMjQ3jOKyac5l-GB9zn-on2QtUUCWqmL5-mhTA0H0aOnkzkSvbnAN7c38%0D%0Aq-VfYyBlO21nk3IS4lU6-I7kTXSmDgQWpltSOwrQ_NFoFX6ylfxKGMF-NiNu0a69bSgYHtczTfjJ%0D%0ANanOIXJ6tX9LjfbwVLedlofZJ1Ar-lskr03OauevosiXa3dFcy_lHA%0D%0A
    
    HTTP/1.1 200 OK
    Date: Fri, 02 Jan 2015 10:09:52 GMT
    Server: Server
    Content-Type: text/html;charset=UTF-8
    Content-Language: en-US
    Vary: Accept-Encoding,User-Agent
    P3P: policyref="http://i.imdb.com/images/p3p.xml",CP="CAO DSP LAW CUR ADM IVAo IVDo CONo OTPo OUR DELi PUBi OTRi BUS PHY ONL UNI PUR FIN COM NAV INT DEM CNT STA HEA PRE LOC GOV OTC "
    Transfer-Encoding: chunked
    
    
    GET /name/nm0001104/?ref_=tt_ov_dr HTTP/1.1
    User-Agent: Mozilla/5.0 (Windows NT 6.1; rv:12.0a2) Gecko/20120203 Firefox/12.0a2
    Host: www.imdb.com
    Accept: */*
    Referer: http://www.imdb.com/title/tt0111161/
    Cookie: session-id-time=1577873387; session-id=875-2962809-9988242; uu=BCYqU413ogo5lQUkcAXoVeJmE3_HPLCKcXGSLTpmq1gwp4NArPHumf1OA0nEjxtfpSgAhriWI8SZ%0D%0AxGuLGIFV0w6o9p5UcmqYeymKNLqcGR96VuBGWQx9uR9b-YXKiGRhckOB9l8-Gub2_nV4Cr1jEx5n%0D%0AEQW0gBslmTgXWO8P6QtiOoromkAVW2gSg2CKsFzmxEwaUnwfF0mPTk5S4hAyDGX6b3u4sKcUdY8b%0D%0AMwutzBnqLg5bhTtRH_N1iJvcowYOHvBsu66RasCUfsoCIbq0vsi5TA%0D%0A
    
    HTTP/1.1 200 OK
    Date: Fri, 02 Jan 2015 10:09:52 GMT
    Server: Server
    Content-Type: text/html;charset=UTF-8
    Content-Language: en-US
    Vary: Accept-Encoding,User-Agent
    P3P: policyref="http://i.imdb.com/images/p3p.xml",CP="CAO DSP LAW CUR ADM IVAo IVDo CONo OTPo OUR DELi PUBi OTRi BUS PHY ONL UNI PUR FIN COM NAV INT DEM CNT STA HEA PRE LOC GOV OTC "
    Transfer-Encoding: chunked
    
    
    GET /name/nm0000338/?ref_=tt_ov_dr HTTP/1.1
    User-Agent: Mozilla/5.0 (Windows NT 6.1; rv:12.0a2) Gecko/20120203 Firefox/12.0a2
    Host: www.imdb.com
    Accept: */*
    Referer: http://www.imdb.com/title/tt0068646/
    Cookie: session-id-time=1577873387; session-id=275-4555266-6048628; uu=BCYvaE68PNmbJBJIV-Sz2v8UPBqi6kwq_am2h2sdD4PYcsJwcEH5PTV8qYTOQtvNPccARMkQ-vdJ%0D%0ApN3nKrQ_XDCKkj6YgLHB8RzbD7qhp-K63g6NrdSOX835OV-rjegqGcB5uiV_4e93LSYU891M2xVh%0D%0A04mOlgS_RR-F83RDZc4ECgM3RJ192zx-6Ug2buvNPIN0Mk5XuVlSuB8S5snWJWZJW7seTdN2l56S%0D%0Au545KJlSaMZEb49p1RnQ9hUczhmLRj341znSeoYaLq0TfH4eQPAiEw%0D%0A
    
    HTTP/1.1 200 OK
    Date: Fri, 02 Jan 2015 10:09:52 GMT
    Server: Server
    Content-Type: text/html;charset=UTF-8
    Content-Language: en-US
    Vary: Accept-Encoding,User-Agent
    P3P: policyref="http://i.imdb.com/images/p3p.xml",CP="CAO DSP LAW CUR ADM IVAo IVDo CONo OTPo OUR DELi PUBi OTRi BUS PHY ONL UNI PUR FIN COM NAV INT DEM CNT STA HEA PRE LOC GOV OTC "
    Transfer-Encoding: chunked


Result:
Array
(
    [6] => Array
        (
            [title] => "The Dark Knight"
            [url] => http://www.imdb.com/title/tt0468569/
            [director] => Christopher Nolan
            [director_awards] => Nominated for 3 Oscars.
        )

    [4] => Array
        (
            [title] => "The Godfather"
            [url] => http://www.imdb.com/title/tt0068646/
            [director] => Francis Ford Coppola
            [director_awards] => Won 5 Oscars.
        )

    [1] => Array
        (
            [title] => "The Shawshank Redemption"
            [url] => http://www.imdb.com/title/tt0111161/
            [director] => Frank Darabont
            [director_awards] => Nominated for 3 Oscars.
        )

)
