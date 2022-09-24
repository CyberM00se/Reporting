# Command Injection Vulnerabilities

Command injection (or OS Command Injection) is a type of injection where software that constructs a system command using externally influenced input does not correctly neutralize the input from special elements that can modify the initially intended command.

{% embed url="https://cheatsheetseries.owasp.org/cheatsheets/OS_Command_Injection_Defense_Cheat_Sheet.html" %}

```php
<form id="logform" method="post">
<div>Search Term: <input type="text" name="search"><div>
</select>
<div class="full-width"></br>
  <button type="submit">Search</button>
</div>
</form>
<?php
if(isset($_POST['search'])) {
  $searchterm=$_POST['search'];
  echo "<div>";
  echo "<h1>Searchterm:" . $searchterm . "</h1>";
  echo "</div>";

  echo "<pre>";
  passthru("cat /usr/share/wordlists/rockyou.txt | grep " . $searchterm);
  echo "</pre>";
}
?>
```

This code is a grepper program that will grep the target file (in this case rock you) we can exploit the text box included in the file.

In order to run your own commands you need to break the input (since its not sanitized) by using the ;

For example

<figure><img src="https://lh3.googleusercontent.com/ZljNjAJ-QFrIrYVdvtC9yeQ7LhzSkMNOUf7grElE4IL5CNHEdKs_ULv_a6laQ2ywEE8zM7l8ijVD0XpR8p6Cqkg1GfuN9iyTyFcNVyxCLTbJDZ8K52xCmaPRByFmJcpn_EkLCg6dPMjmFU714jnYAf1ue2U-DDEPRdsUK7vlQnXE7BKm3W5yhfIkGg" alt=""><figcaption></figcaption></figure>

