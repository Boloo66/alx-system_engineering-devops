## POSTMORTEN (INCIDENT REPORT)

* summary of incident:
    Upon the release of Holberton School's System Engineering & DevOps project 0x19, approximately 00:07 Pacific Standard Time (PST), an outage occurred on an isolated Ubuntu 14.04 container running an Apache web server. GET requests on the server led to 500 Internal Server Error's, when the expected response was an HTML file defining a simple Holberton WordPress site.
* timeline:
    Noticed: 18:09
    Resolved: 20:25
* root cause:
    GET requests on the server led to 500 Internal Server Error's, when the expected response was an HTML file defining a simple Holberton WordPress site.
* resolution and recovery:
    Checked running processes using ps aux. Two apache2 processes - root and www-data - were properly running.

    Looked in the sites-available folder of the /etc/apache2/ directory. Determined that the web server was serving content located in /var/www/html/.

    In one terminal, ran strace on the PID of the root Apache process. In another, curled the server. Expected great things... only to be disappointed. strace gave no useful information.

    Repeated step 3, except on the PID of the www-data process. Kept expectations lower this time... but was rewarded! strace revelead an -1 ENOENT (No such file or directory) error occurring upon an attempt to access the file /var/www/html/wp-includes/class-wp-locale.phpp.

    Looked through files in the /var/www/html/ directory one-by-one, using Vim pattern matching to try and locate the erroneous .phpp file extension. Located it in the wp-settings.php file. (Line 137, require_once( ABSPATH . WPINC . '/class-wp-locale.php' );).

    Removed the trailing p from the line.

    Tested another curl on the server. 200 A-ok!


* corrective and preventive measures
    Wrote a Puppet manifest to automate fixing of the error.