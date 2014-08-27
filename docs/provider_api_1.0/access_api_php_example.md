# Exempel i PHP

Com Hem har tagit fram en PHP implementation av Access API.

## PHP exempel

```php
<?
\$if_modified_since = strtotime(\$_SERVER['HTTP_IF_MODIFIED_SINCE']);

if ((is_int(\$if_modified_since) && \$if_modified_since >= 0) || !isset(\$_SERVER['HTTP_IF_MODIFIED_SINCE'])) {

	\$db_conn = mysql_connect('localhost','username', 'password');
	mysql_select_db('database');

	if (is_int(\$if_modified_since)) {
		\$query = "SELECT * FROM feasibility WHERE last_modified > FROM_UNIXTIME(\$if_modified_since)";
	} else {
		\$query = "SELECT * FROM feasibility";
	}

	\$result = mysql_query(\$query);
	if (\$result) {
		\$rows = mysql_num_rows(\$result);
		\$entries = new SimpleXMLElement('<Entries/>');
		\$entries->addChild('Count', mysql_num_rows(\$result));

		for (\$i = 0; \$i < mysql_num_rows(\$result); \$i++) {
			\$row = mysql_fetch_array(\$result, MYSQL_ASSOC);
			\$entry = \$entries->addChild('Entry');
			\$entry->addChild('AccessId', \$row["access_id"]);
			\$access = \$entry->addChild('Access');
			\$access->addChild('StreetName', \$row["street_name"]);
			\$access->addChild('StreetNumber', \$row["street_number"]);
			\$access->addChild('StreetLittera', \$row["street_littera"]);
			\$access->addChild('PostalCode', \$row["postal_code"]);
			\$access->addChild('City', \$row["city"]);
			\$access->addChild('CountryCode', \$row["country_code"]);
			\$access->addChild('PremisesType', \$row["premises_type"]);
			\$access->addChild('MduApartmentNumber', \$row["mdu_apartment_number"]);
			\$access->addChild('MduDistinguisher', \$row["mdu_distinguisher"]);
			\$access->addChild('Sellable', (is_null(\$row["sellable"])?"NO":\$row["sellable"]));
			\$access->addChild('Activatable', (is_null(\$row["activatable"])?"NO":\$row["activatable"]));
			\$access->addChild('FiberConverter', \$row["fiber_converter"]);
			\$access->addChild('CpeSwitch', \$row["cpe_switch"]);
			\$access->addChild('CpeRouter', \$row["cpe_router"]);
			\$access->addChild('Option82', \$row["option_82"]);
			\$access->addChild('Population', \$row["population"]);
			if (!isset(\$last_modified) || strtotime(\$row["last_modified"]) > \$last_modified)
				\$last_modified = strtotime(\$row["last_modified"]);
		}
		header("Content-Type: application/xml");
		header("Expires: -1");

		if (\$rows == 0) {
			header("HTTP/1.1 304 Not Modified", true, 304);
		} else {
			header("Last-Modified: ".gmdate("D, d M Y H:i:s", \$last_modified)." GMT");
			print(html_entity_decode(\$entries->asXML(), ENT_NOQUOTES, 'UTF-8'));
		}
	} else {
		header("HTTP/1.1 500 Internal Server Error", true, 500);
	}

} else {
	header('HTTP/1.0 400 Bad Request', true, 400);
	print "If-Modified-Since malformed";
}
?>
```

## MySQL schema

```sql
-- MySQL dump 10.9
-- ------------------------------------------------------

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `feasibility`
--

DROP TABLE IF EXISTS `feasibility`;
CREATE TABLE `feasibility` (
  `access_id` varchar(32) NOT NULL default '',
  `street_name` text NOT NULL,
  `street_number` int(11) NOT NULL default '0',
  `street_littera` text,
  `postal_code` int(11) NOT NULL default '0',
  `city` text NOT NULL,
  `country_code` text NOT NULL,
  `premises_type` text NOT NULL,
  `mdu_apartment_number` int(11) default NULL,
  `mdu_distinguisher` text,
  `activatable` date default NULL,
  `sellable` date default NULL,
  `fiber_converter` text,
  `cpe_switch` text,
  `cpe_router` text,
  `option_82` text,
  `population` text,
  `last_modified` timestamp NOT NULL default CURRENT_TIMESTAMP on update CURRENT_TIMESTAMP,
  PRIMARY KEY  (`access_id`),
  KEY `last_modified_index` (`last_modified`)
) ENGINE=MyISAM DEFAULT CHARSET=latin1;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
```
