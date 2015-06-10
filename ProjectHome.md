**Update: I will not upload any updates for this class.**

Please check out my PHP class SEOStats. It includes improved versions of the functions of this class.

Plus: Google functions, Yahoo functions, Majesticseo functions and a frontend for instant use of the class.

22 powerful functions will return website statistics as easy as it comes! Also SEOStats gets regular updates.

Your choice: Use this class, or the way better SEOStats ;-)

http://code.google.com/p/seostats/

**PHP Class AlexaStats**

Simple PHP class to recieve diverse statistics from Alexa Web Services.

**Getting started:**
```
include './class/class.alexa.php';      
 
$obj = new AlexaStats();
$obj->domain = 'yoursite.com';
```

**Global Traffic Rank:**
```
echo $obj->GlobalRank();
```

**Country Traffic Rank:**
```
echo $obj->CountryRank();
```

**Optional Parameter for Stats and Trends:**
```
$obj->period = '6';             // Define date range in months | if not set = 1 month
```

**Daily Pageviews Stats and Trends:**
```
echo $obj->DailyPageviews();
```

**Daily Pageviews Per User Stats and Trends:**
```
echo $obj->DailyPageviewsPerUser();
```

**Reach Stats and Trends:**
```
echo $obj->Reach();
```

**Bounce Rate Stats and Trends:**
```
echo $obj->BounceRate();
```

**Time on Site Stats and Trends:**
```
echo $obj->TimeOnSite();
```

**Search Visits Stats and Trends:**
```
echo $obj->SearchVisits();
```

**Draw Graph:**
```
$obj->type = '1';               // Graph type | if not set = 1
/*
* 1 = Daily traffic rank trend
* 2 = Daily pageviews (percent)
* 3 = Daily pageviews per user
* 4 = Time on site (minutes)
* 5 = Bounce rate (percent)
* 6 = Search visits (percent) 
*/
 
$obj->w = '440';                // Define width of graph | if not set = 660px
$obj->h = '220';                // Define height of graph | if not set = 330px
 
echo $obj->writeGraph();        // Displays graph as image
```

**THE CLASS**
```
<?php
/*
 ======================================================================
 AlexaStats v1.0.3
 
 Simple yet powerfull PHP class to get Statistics from Alexa.
 
 by Stephan Schmitz, info@eyecatch-up.de
 
 Latest version, features, manual and examples:
     http://code.eyecatch-up.de/alexa/
 ----------------------------------------------------------------------
 CHANGELOG
 v1.0.3     18.10.2010    - higher performace by code clean up
                          - more oop
 v1.0.2     17.10.2010    - higher performace while extraction
                            by using htmlsql and snoopy class
                          - removed function getRank
                          - removed function getStats
                          - added function GlobalRank
                          - added function CountryRank
                          - added function DailyTrafficRankTrend
                          - added function DailyPageviews
                          - added function DailyPageviewsPerUser
                          - added function Reach
                          - added function BounceRate
                          - added function TimeOnSite
                          - added function SearchVisits
 v1.0.1     17.10.2010    - changed file_get_contents to curl
 ----------------------------------------------------------------------
 LICENSE

 This program is free software; you can redistribute it and/or
 modify it under the terms of the GNU General Public License (GPL)
 as published by the Free Software Foundation; either version 2
 of the License, or (at your option) any later version.

 This program is distributed in the hope that it will be useful,
 but WITHOUT ANY WARRANTY; without even the implied warranty of
 MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
 GNU General Public License for more details.

 To read the license please visit http://www.gnu.org/copyleft/gpl.html
 ======================================================================
*/

/**
* AlexaStats
* Simple yet powerfull PHP class to get Statistics from Alexa.
*/
class AlexaStats {
// -------------------------------------------------------------------
// CLASS VARS
// -------------------------------------------------------------------
    var $domain;              // Domain to check
    var $period  = '1';       // Date Range in months
    var $type  = '1';         // Graph type 
    var $w = '660';           // Width of graph
    var $h = '330';           // Height of Graph
// -------------------------------------------------------------------
// PRIVATE FUNCTIONS
// -------------------------------------------------------------------
    // -------------------------------------------------------------------
    // Private Function to cURL Alexa site and return as string
    // -------------------------------------------------------------------
    private function alexa(){
      // -------------------------------------------------------------------
      // Save alexa result page to string using curl
      // -------------------------------------------------------------------
      $ch = curl_init();
      curl_setopt($ch,CURLOPT_URL,'http://www.alexa.com/siteinfo/'.$this->domain);
      curl_setopt($ch,CURLOPT_RETURNTRANSFER,1);
      curl_setopt($ch,CURLOPT_CONNECTTIMEOUT,5);
      $str = curl_exec($ch);
      // -------------------------------------------------------------------
      // Correct image paths
      // -------------------------------------------------------------------
      $str = str_replace('/images/', 'http://www.alexa.com/images/', $str);
      // -------------------------------------------------------------------
      // Return string
      // -------------------------------------------------------------------
      return $str;    
    } // End of private function alexa
    // -------------------------------------------------------------------
    // Private Function to extract data using htmlsql and snoopy class
    // -------------------------------------------------------------------
    private function extract($atr,$id){
      // -------------------------------------------------------------------
      // External classes
      // -------------------------------------------------------------------
      include_once("ext/snoopy.class.php");
      include_once("ext/htmlsql.class.php");
      // -------------------------------------------------------------------
      // Extract information
      // -------------------------------------------------------------------
      $wsql = new htmlsql();
      if (!$wsql->connect('string', $this->alexa())){
        print 'Error while connecting: ' . $wsql->error;
        exit;
      }
      if (!$wsql->query('SELECT * FROM div WHERE $'.$atr.' == "'.$id.'"')){
        print "Query error: " . $wsql->error; 
        exit;
      }
      $row = $wsql->fetch_array();      
      // -------------------------------------------------------------------
      // Return string
      // -------------------------------------------------------------------
      return $row[0]['text'];
    } // End of private function extract
// -------------------------------------------------------------------
// PUBLIC FUNCTIONS
// -------------------------------------------------------------------
    // -------------------------------------------------------------------
    // Public Function to get the global Alexa traffic rank
    // -------------------------------------------------------------------
    public function GlobalRank(){
      // Call internal function and return result     
      return $this->extract('class','data up');      
    } // End of function GlobalRank
    // -------------------------------------------------------------------
    // Function to get the global Alexa traffic rank
    // -------------------------------------------------------------------
    public function CountryRank(){
      // Call internal function and return result     
      return $this->extract('class','data'); 
    } // End of function CountryRank
    // -------------------------------------------------------------------
    // Function to get the daily traffic rank trend stats
    // -------------------------------------------------------------------
    public function DailyTrafficRankTrend(){
      // Call internal function and return result     
      return $this->extract('id','rank'); 
    } // End of function DailyTrafficRankTrend
    // -------------------------------------------------------------------
    // Function to get the daily pageviews stats
    // -------------------------------------------------------------------
    public function DailyPageviews(){
      // Call internal function and return result     
      return $this->extract('id','pageviews'); 
    } // End of function DailyPageviews    
    // -------------------------------------------------------------------
    // Function to get the daily pageviews per user stats
    // -------------------------------------------------------------------
    public function DailyPageviewsPerUser(){
      // Call internal function and return result     
      return $this->extract('id','pageviews_per_user'); 
    } // End of function DailyPageviewsPerUser
    // -------------------------------------------------------------------
    // Function to get the reach stats
    // -------------------------------------------------------------------
    public function Reach(){
      // Call internal function and return result     
      return $this->extract('id','reach'); 
    } // End of function Reach
    // -------------------------------------------------------------------
    // Function to get the bounce rate stats
    // -------------------------------------------------------------------
    public function BounceRate(){
      // Call internal function and return result     
      return $this->extract('id','bounce'); 
    } // End of function BounceRate
    // -------------------------------------------------------------------
    // Function to get the time on site stats
    // -------------------------------------------------------------------
    public function TimeOnSite(){
      // Call internal function and return result     
      return $this->extract('id','time_on_site'); 
    } // End of function TimeOnSite
    // -------------------------------------------------------------------
    // Function to get the search visit stats
    // -------------------------------------------------------------------
    public function SearchVisits(){
      // Call internal function and return result     
      return $this->extract('id','search'); 
    } // End of function SearchVisits
    // -------------------------------------------------------------------
    // Function to write statistics as graph -> returns html string
    // -------------------------------------------------------------------
    public function writeGraph(){
      // -------------------------------------------------------------------
      // Define graph type
      // -------------------------------------------------------------------  
      switch($this->type) {
				case 1:
          $type = 't'; // Daily traffic rank trend				
					break;
				case 2:
          $type = 'p'; // Daily pageviews (percent)					
					break;
				case 3:
          $type = 'u'; // Daily pageviews per user					
					break;
				case 4:
          $type = 's'; // Time on site (minutes)					
					break;
				case 5:
          $type = 'b'; // Bounce rate (percent)					
					break;
				case 6:
          $type = 'q'; // Search visits (percent)					
					break;
				default:break;
      }
      // -------------------------------------------------------------------
      // Build html output
      // -------------------------------------------------------------------    
      $graph = '<img src="http://traffic.alexa.com/graph?&w='.$this->w.'&h='.$this->h.'&o=f&c=1&y='.$type.'&b=ffffff&n=666666&r='.$this->period.'m&u='.$this->domain.'" />';
      return $graph;
    } // End of function writeGraph
} // End of class AlexaStats v1.0.3 by Stephan Schmitz @ http://code.eyecatch-up.de/alexa/
?>
```