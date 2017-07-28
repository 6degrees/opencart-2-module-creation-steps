# opencart-2-module-creation-steps
How to Create Opencart 2 Admin Modules/Extension

## Steps
* Create the following parts:
    * Controller `admin/Controller/folder/extension_name.php`
	* Module `admin/model/folder/extension_name.php`
	* View file (.tpl) `admin/view/template/folder/extension_name.tpl`
	* Language file (optional) `admin/language/arabic/folder/module_name.php`
* Create the link on the menu
	* Template `/admin/view/template/common/menu.tpl`
	  Add your entry where you want your menu Item to appear
	  Use the following syntax:
	  ```php
	  <li><a href="<?php echo $duplicate; ?>"><?php echo "Duplicate Orders"; ?></a></li>
	  ```
	* Menu file `/admin/controller/common/menu.php`
	  Add your entry under whatever you want
	  Use the following syntax:
	  ```php
	  $data['duplicate'] = $this->url->link('report/duplicate', 'token=' . $this->session->data['token'], 'SSL');
	  ```
