API
===

.. autosummary::
   :toctree: generated

                           AltaUserPoints Developers API documentation            
        
Introduction AltaUserPoints - API integration in a Third party component (advanced)This documentation is priorly dedicated to developers and anyone with a sufficient knowledge of PHP language and Joomla! components development.  Licence: AltaUserPoints is released under license GNU/GPL License. Author: Bernard Gilly – Adrien Roussel - This project started as AltaUserPoints back in 2008.

## Plugin/Rule creation

A plugin creation (new rule creation for a 3rd party component) is done in 2 steps

### Step1 - API insertion in a component

Just insert the following API in the component code where needed. The best is to make it follow a user action that could give the users some points or take some. For example : in a comment system component or in a forum, just add the API after the comment or topic INSERT query in the database.

### Basic API

`$api_AUP = JPATH_SITE.'/components/com_altauserpoints/helper.php';
if ( file_exists($api_AUP))
{
require_once $api_AUP;
AltaUserPointsHelper::newpoints( 'function_name' );
}`function_name is the name of the rule that will be used to attribute points to the current user (if logged in). For each AltaUserPoints integrated rule (system rules), names syntax is as follows: example: sysplgaup_newregistered for new users points attributionIt is convenient to keep the same name syntax for third party components plugin as follows:plgaup_functionnameExample: plgaup_newcomment or plgaup_newtopic for a comment system or forum API integration. To name a rule that would give points when adding a new topic in Kunena: plgaup_kunena_topic_create.Keep in mind that this method only gives points to the current user logged in. This is the Basic API.  

### Attribute points to another user than the current user

To give points to anothe user than the one connected, only the user id is required. To get his AltaUserPoints (AUPID) Identity, we just need to use the getAnyUserReferreID(). This method is the one used to give points to an article author when the article is being read by someone on the site. ```$api_AUP = JPATH_SITE.'/components/com_altauserpoints/helper.php';
 ``if ( file_exists($api_AUP))
 ``{
 require_once $api_AUP;
 $aupid = AltaUserPointsHelper::getAnyUserReferreID( $userID );
 if ( $aupid )
 AltaUserPointsHelper::newpoints( 'function_name', $aupid );
 }``` 

### Prevent from attributing points more than once for the same action

To avoid that a user would get points many times for something allready done, we can add a reference key. When calling the AltaUserPointsHelper::newpoints function, a pre check is done on this reference key. This method is used in the rule where a user reading an article would give points to the author. `$api_AUP = JPATH_SITE.'/components/com_altauserpoints/helper.php';
 if ( file_exists($api_AUP))
 {
 require_once $api_AUP;
 $keyreference = AltaUserPointsHelper::buildKeyreference('function_name', ‘item id’ );
 AltaUserPointsHelper::newpoints( 'function_name', '', $keyreference);
 }`  

### Adding information datas

To add information datas to be displayed in the action details, just add a new parameter as follows:   ```$api_AUP = JPATH_SITE.'/components/com_altauserpoints/helper.php';
 ``if ( file_exists($api_AUP))
 {
 require_once $api_AUP;
 $keyreference = AltaUserPointsHelper::buildKeyreference('function_name', ‘item id’ );
 AltaUserPointsHelper::newpoints( function_name','', $keyreference,'information_datas');
 }``` 

### Using different amounts of points on the same rule

A component might also need to add or to take points for different amounts. For example, when buying goods with points. Products have different prices, a fixed amount in the rule would'nt make it. The API $randompoints parameter comes instead of the amount of points set in the rule. It can be negative in case of purchases or penalities.  ```$api_AUP = JPATH_SITE.'/components/com_altauserpoints/helper.php';
 ``if ( file_exists($api_AUP))
 {
 require_once $api_AUP;
 $keyreference = AltaUserPointsHelper::buildKeyreference('function_name', ‘item id’ );
 AltaUserPointsHelper::newpoints( function_name','', $keyreference,'',-1450);
 }``` 

### Get the result from a successfull operation

In a more advanced code, if the component routine needs to know if the operation has been successfull or not, (enough amount of points for a purchase in a user account), we can add a 'feedback' parameter. It has a Boolean type value. Code example: ```$api_AUP = JPATH_SITE.'/components/com_altauserpoints/helper.php';
 ``if ( file_exists($api_AUP))
 {
 require_once $api_AUP;
$keyreference=AltaUserPointsHelper::buildKeyreference('plgaup_purchasewithvirtuemart', $transactionID );
 if (AltaUserPointsHelper::newpoints( 'plgaup_purchasewithvirtuemart', '', $keyreference, 'Product reference: AM-5245', -1290, true))
 {
 [... code continued ...] 
 }
 }``` 

### Remove the constraint on the type of user

In a customized code component, you can force and remove the constraint on a rule to the user level by adding the parameter *force = 1*. The existing rule will be available now for *guest, registered and special*. 

### Display an additional system message on frontend

you can display a specific message on frontend by adding the parameter frontmessage=”You custom message”. API full implementation   ```AltaUserPointsHelper::newpoints(
string$pluginfunction,
 [string$AUPIDotheruser = ''],
 [string$keyreference = ''],
 [string$data = ''],
 [integer$randompoints = 0],
 ``[boolean$feedback = false],
 [integer$force=0],
 [string$frontmessage='']
 );``` Note: If the operation is a points substraction, the account has to have at least the same amount of points. If not, a notice warns the user that he doe'snt have enough points to complete the action (by default). You can set up general parameter in the configuration of AltaUserPoints, in backend administrator to allows your users to have a negative account.

## Step 2 - XML file creation

Then developers have to create an xml file to make easier the installation process in the AltaUserPoints component. This xml file has to be utf-8 encoded (required). All developers of third party extensions for Joomla! can add directly at the root of their frontend component a unique xml file containing all the rules for a single component. Deveoper has to respect the ordering and tags: structure example: [altauserpoints_rule.xml](images/stories/documentation/altauserpoints/altauserpoints_rule.xml) Tag “component” is the name of the third component like “com_kunena” or other. As it is the same component, it is worth repeating for each rule in the tag “rule”. Administrator of the website which install a third component with this xml file can autodetect directly from the button “auto-detect plugins” in control panel of AltaUserPoints. This xml file has to be utf-8 encoded (required) but not be zipped! Just put this file at the root of frontend component folder or plugin or module and include this file in your installer extension. This file must be named exactly as follows: altauserpoints_rule.xml

### Code list for categories :

ar -> articles cd -> coupons  co -> community  fo -> forums/comments li -> links mu -> music  ot -> other  ph -> photo po -> polls pu -> purchase re -> recommend/send to a friend sh -> shopping su -> private sy -> system rules us -> users vi -> video  NOTE : Optionally, you can avoid this step manually by filling all the fields in the creation of a new rule directly in the rule manager (button 'New' in toolbar). 

## Plugin/Rule installation

-   auto-detect xml file at the root of frontend component: Click on the button “Auto-detect plugins” in the control panel of AltaUserPoints after installation of third component, plugin or module. Check regularly or periodically by clicking on this button.

## Using AltaUserPoints informations in a third party component

You can use easily the profil informations of a user directly in a third component.  => Before using a function, you must include the API at least once in your page like this: `$api_AUP = JPATH_SITE.'/components/com_altauserpoints/helper.php';
if ( file_exists($api_AUP)) { require_once $api_AUP; }`

### Profil informations

To get the entire profil information, just use the function getUserInfo();  Just use the referreid of AltaUserPoints user or the joomla ID of the user (Id of Joomla users table).  Use the first method with the referreid to get user Information profile like this :  `AltaUserPointsHelper::getUserInfo($referrerid );`    If you do not have the referreid, you can use the ID of user in second parameter like this : `$user = JFactory::getUser();
 $userid = $user->id ;
 $profil = AltaUserPointsHelper::getUserInfo ( '', $user->id );` Then you can get the following user informations: $profil->name $profil->username $profil->registerDate $profil->lastvisitDate $profil->email $profil->referreid $profil->points $profil->max_points $profil->last_update $profil->referraluser $profil->referrees $profil->blocked $profil->birthdate $profil->avatar $profil->levelrank $profil->leveldate $profil->gender $profil->aboutme $profil->website  $profil->phonehome $profil->phonemobile $profil->address $profil->zipcode $profil->city $profil->country $profil->education $profil->graduationyear $profil->job $profil->facebook $profil->twitter $profil->icq $profil->aim $profil->yim  $profil->msn $profil->skype  $profil->gtalk  $profil->xfire  $profil->profileviews 

### Get AUP avatar of user

Display the image of avatar from AltaUserPoints.  `$avatar = AltaUserPointsHelper:: getAupAvatar( 
$userid, 
[$linktoprofil=0],
[ $width=48], 
[$height=48], 
[$class=''], 
[$otherprofileurl=''] 
) 
echo $avatar ;`   if $linktoprofil set to 1, display avatar with the link to the AUP profil of this user. 

### Get link to AUP user profil

Get the url to show the profil of user. `$linktoAUPprofil = AltaUserPointsHelper::getAupLinkToProfil($userid);`

### Get link to AUP users list

Get the url to show the list of users with points etc… `$linktoAUPusersList = AltaUserPointsHelper:: getAupUsersURL();`  

### Get avatar Path of a user

Get the path avatar path of a specific user `$avatarPath = AltaUserPointsHelper:: getAvatarPath( $userid );`  

### Get avatar Live Path of a user

Get the live url avatar path of a specific user \`$avatarLivePath = AltaUserPointsHelper:: getAvatarLivePath( $userid );

\```

### Get the medals list of a user

`$medalslistuser = getUserMedals($referrerid);` or `$medalslistuser = getUserMedals('', $userid);` return $medallistuser->id  return $medallistuser->rank (name of medal)  return $medallistuser->description (reason for awarded)  return $medallistuser->icon (name file icon) return $medallistuser->image (name file image) Complete example to display large image of medals: ```$user = JFactory::getUser();
``$userid = $user->id ;
 if(!defined("_AUP_MEDALS_PATH"))
 {
 define('_AUP_MEDALS_PATH', JURI::root() . 'components/com_altauserpoints/assets/images/awards/large/’);
 }
 if(!defined("_AUP_MEDALS_LIVE_PATH"))
 { 
define('_AUP_ MEDALS _LIVE_PATH', JURI::base(true) . '/components/com_altauserpoints/assets/images/awards /large/');
 }
 $medalslistuser = AltaUserPointsHelper::getUserMedals( '', $userid );
 for each ( $medalslistuser as $medallistuser )
 {
 echo '<img src="'. _AUP_MEDALS_LIVE_PATH'.$medallistuser->image . '" alt="" />';
 }``` 

### Get the ReferreID of any user

`$referreid = AltaUserPointsHelper::getAnyUserReferreID( $userID );`   

### Get the current total points of any user

Use the first method with the referreid to get the total of points `$totalPoints = AltaUserPointsHelper::getCurrentTotalPoints( $referrerid );

` If you have not the referreid, you can use the ID of user (joomla) in second parameter like this :  `$user = JFactory::getUser();
 $userid = $user->id ;
 $totalPoints = AltaUserPointsHelper::getCurrentTotalPoints( '', $userid );` 

### Get the list of activities

`$listActivities = AltaUserPointsHelper::getListActivity($type='all', $userid='all', $numrows=0);
` $params $type = all | positive | negative  $params $userid = all or unique userID  $params $limit = int (0 by default)  example-1 -> ------------------------------------------------------------------------- example-1 -> $listActivities = AltaUserPointsHelper::getListActivity('all', 'all');  example-1 SAME AS -> $listActivities = AltaUserPointsHelper::getListActivity();  example-1 -> show all activities with pagination, positive and negative points of activity for all users  ----------------------------------------------------------------------------------- example-2 -> ------------------------------------------------------------------------- example-2 -> $user = JFactory::getUser();  example-2 -> $userID = $user->id;  example-2 -> $listActivities = AltaUserPointsHelper::getListActivity('positive',$userID,20);  example-2 -> show only positive points of activity for the current user logged in and show only 20 rows of recent activities  Returns an array or $rows List of available fields : insert_date  referreid  points (of this activity)  datareference  rule_name  plugin_function  category

### Get the next user rank

`$nextrankinfo = AltaUserPointsHelper::getNextUserRank($referrerid='’, $userid='0', currentrank);`  return $nextrankinfo->id  return $nextrankinfo->rank (name of rank)  return $nextrankinfo->description (description of rank)  return $nextrankinfo->levelpoints (level points to reach this rank)  return $nextrankinfo->typerank (return 0)  return $nextrankinfo->icon (name file icon)  return $nextrankinfo->image (name file image)

### Get version of AUP

`$num_aup_version = AltaUserPointsHelper::getAupVersion();` Returns the current version of AltaUserPoints like -> 1.0.0  

## AltaUserPoints is open for third component

Create your own plugin for AltaUserPoint ! Plugins provide functions which are associated with trigger events.  Available events in AltaUserPoints:  - onBeforeInsertUserActivityAltaUserPoints - onUpdateAltaUserPoints - onAfterUpdateAltaUserPoints - onChangeLevelAltaUserPoints - onUnlockMedalAltaUserPoints - onGetNewRankAltaUserPoints - onResetGeneralPointsAltaUserPoints - onBeforeDeleteUserActivityAltaUserPoints - onAfterDeleteUserActivityAltaUserPoints - onBeforeDeleteAllUserActivitiesAltaUserPoints - onAfterDeleteAllUserActivitiesAltaUserPoints - onBeforeMakeRaffleAltaUserPoints - onAfterMakeRaffleAltaUserPoints  You can see the example file /plugins/altauserpoints/example_plugin_aup.php in your Joomla directory with parameters for each functions.\
\
\
                            [altauserpoints](/extensions/index.php?option=com_tags&view=tag&id=4-altauserpoints),            [alphauserpoints](/extensions/index.php?option=com_tags&view=tag&id=5-alphauserpoints)
\
\
                



                                * Created on 15 January 2016.
                
                                * Last updated on 01 June 2016.
                
                
            
            
            
            
            




                
                            
                    
                
                
                
