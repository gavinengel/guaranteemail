CAPTCHAs.  Spam.

**I’m not quite sure which I hate more.**

Sure, Spam is an extremely annoying and objectionable.  It’s definitely not something you want your mom to have to see.

On the other hand, the popular method of preventing Spam, CAPTCHAs, can be _just an annoying_.  Your website can easily lose submissions because CAPTCHAs annoy potential pen pals.

Enter: **Guaranteemail**.  This is a very simple php class (actually it also wraps around other included classes) which can effortlessly help you block spammers.  I value simplicity in my work, and this class keeps it as simple as humanly possible for your app.

Here is what **Guaranteemail** does in a nutshell to verify email addresses:

  1. email address syntax (missing `@`, missing `.`, etc)
  1. the domain name of the email address is registered and responding
  1. the ip address of the email host is not on a major DNSBL
  1. the ip address of the visitor is not on a major DNSBL
  1. for certain email providers, namely Gmail and Yahoo Mail, we can check the exact mailbox with SMTP validation

Here is example usage:

```
<?php
  require 'Guaranteemail.php';
  $Guaranteemail = new Guaranteemail;
  $Guaranteemail->email = 'example@example.com';
  if ($Guaranteemail->verify()) {
    echo "valid!";
  } else {
    echo "invalid!";
  }
?>
```

That test utilizes most of what is offered in this class.  A more complete example, which shows off the DNSBL checking (currently using http://projecthoneypot.org and http://www.spamhaus.org), loops addresses, and error & debug reporting:

```
<?php
  $emails = array(
    'example@gmail.com',
    'example@yahoo.com',
    'example@example.com',
  );

  require 'Guaranteemail.php';
  $Guaranteemail = new Guaranteemail;

  // use your free ProjectHoneypot.org access key, 
  // this will check to see if visitor ($_SERVER['REMOTE_ADDR']) 
  // is a known spammer.
  $Guaranteemail->http_bl_access_key = 'abcdefghijkl';

  // Loop through as many emails as you want!  There is  
  // extensive cacheing done in order to make as few 
  // network api calls as necessary.
  foreach ($emails as $email) {
    $Guaranteemail->email = $email;
    echo "<br>$email is ... ";
    if ($Guaranteemail->verify()) {
      echo "valid!";
    }
    else {
      echo "invalid!";
      echo "<p>errors found:</p>";
  
      // You will probably want to know which check caused 
      // this address to fail
      echo "<pre>";
      print_r($Guaranteemail->errors);
      echo "</pre>";
    }

    // Oftentimes, your server ip address is listed on a  
    // greylist (like Yahoo's), so printing debug is the 
    // way to determine this.
    echo "<p>debug messages:</p>";
    echo "<pre>";
    print_r($Guaranteemail->debug_messages);
    echo "</pre>";

  }
?>
```