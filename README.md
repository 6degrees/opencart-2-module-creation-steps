# opencart-2-module-creation-steps
How to Create Opencart 2 Admin Modules/Extension

## Steps
* Create the following parts:
    * Controller `admin/controller/folder/extension_name.php`
	  Use the following initial content
	  ```php
	  <?php
		// admin/controller/module/recent_products.php
		class ControllerFolderExtensionName extends Controller {
		    private $error = array();

		    public function index() {
				# Load Language File
		        $this->load->language('module/recent_products');

				# Set Page Title
		        $this->document->setTitle($this->language->get('heading_title'));

				# Load the model for database interactions
		        $this->load->model('extension/module');

				# Check if it is a post request
		        if (($this->request->server['REQUEST_METHOD'] == 'POST') && $this->validate()) {
		            if (!isset($this->request->get['module_id'])) {
		                $this->model_extension_module->addModule('recent_products', $this->request->post);
		            } else {
		                $this->model_extension_module->editModule($this->request->get['module_id'], $this->request->post);
		            }

		            $this->session->data['success'] = $this->language->get('text_success');

		            $this->response->redirect($this->url->link('extension/module', 'token=' . $this->session->data['token'], 'SSL'));
		        }

				# Set page data and language variables
		        $data['heading_title'] = $this->language->get('heading_title');

		        $data['text_edit'] = $this->language->get('text_edit');
		        $data['text_enabled'] = $this->language->get('text_enabled');
		        $data['text_disabled'] = $this->language->get('text_disabled');

		        $data['entry_name'] = $this->language->get('entry_name');
		        $data['entry_limit'] = $this->language->get('entry_limit');
		        $data['entry_status'] = $this->language->get('entry_status');

		        $data['button_save'] = $this->language->get('button_save');
		        $data['button_cancel'] = $this->language->get('button_cancel');

				# Check for errors
		        if (isset($this->error['warning'])) {
		            $data['error_warning'] = $this->error['warning'];
		        } else {
		            $data['error_warning'] = '';
		        }

		        if (isset($this->error['name'])) {
		            $data['error_name'] = $this->error['name'];
		        } else {
		            $data['error_name'] = '';
		        }

				# Setup the breadcrumbs
		        $data['breadcrumbs'] = array();

		        $data['breadcrumbs'][] = array(
		            'text' => $this->language->get('text_home'),
		            'href' => $this->url->link('common/dashboard', 'token=' . $this->session->data['token'], 'SSL')
		        );

		        $data['breadcrumbs'][] = array(
		            'text' => $this->language->get('text_module'),
		            'href' => $this->url->link('extension/module', 'token=' . $this->session->data['token'], 'SSL')
		        );

		        if (!isset($this->request->get['module_id'])) {
		            $data['breadcrumbs'][] = array(
		                'text' => $this->language->get('heading_title'),
		                'href' => $this->url->link('module/recent_products', 'token=' . $this->session->data['token'], 'SSL')
		            );
		        } else {
		            $data['breadcrumbs'][] = array(
		                'text' => $this->language->get('heading_title'),
		                'href' => $this->url->link('module/recent_products', 'token=' . $this->session->data['token'] . '&module_id=' . $this->request->get['module_id'], 'SSL')
		            );
		        }


		        if (!isset($this->request->get['module_id'])) {
		            $data['action'] = $this->url->link('module/recent_products', 'token=' . $this->session->data['token'], 'SSL');
		        } else {
		            $data['action'] = $this->url->link('module/recent_products', 'token=' . $this->session->data['token'] . '&module_id=' . $this->request->get['module_id'], 'SSL');
		        }

		        $data['cancel'] = $this->url->link('extension/module', 'token=' . $this->session->data['token'], 'SSL');

		        if (isset($this->request->get['module_id']) && ($this->request->server['REQUEST_METHOD'] != 'POST')) {
		            $module_info = $this->model_extension_module->getModule($this->request->get['module_id']);
		        }

		        if (isset($this->request->post['name'])) {
		            $data['name'] = $this->request->post['name'];
		        } elseif (!empty($module_info)) {
		            $data['name'] = $module_info['name'];
		        } else {
		            $data['name'] = '';
		        }

		        if (isset($this->request->post['limit'])) {
		            $data['limit'] = $this->request->post['limit'];
		        } elseif (!empty($module_info)) {
		            $data['limit'] = $module_info['limit'];
		        } else {
		            $data['limit'] = 5;
		        }

		        if (isset($this->request->post['status'])) {
		            $data['status'] = $this->request->post['status'];
		        } elseif (!empty($module_info)) {
		            $data['status'] = $module_info['status'];
		        } else {
		            $data['status'] = '';
		        }

		        $data['header'] = $this->load->controller('common/header');
		        $data['column_left'] = $this->load->controller('common/column_left');
		        $data['footer'] = $this->load->controller('common/footer');

		        $this->response->setOutput($this->load->view('module/recent_products.tpl', $data));
		    }

		    protected function validate() {
		        if (!$this->user->hasPermission('modify', 'module/recent_products')) {
		            $this->error['warning'] = $this->language->get('error_permission');
		        }

		        if ((utf8_strlen($this->request->post['name']) < 3) || (utf8_strlen($this->request->post['name']) > 64)) {
		            $this->error['name'] = $this->language->get('error_name');
		        }

		        return !$this->error;
		    }
		}
	  ```
	* Module `admin/model/folder/extension_name.php`
	* View file (.tpl) `admin/view/template/folder/extension_name.tpl`
	  Use the following initial content
	  ```html
	  	<!-- admin/view/template/module/recent_products.tpl -->
		<?php echo $header; ?><?php echo $column_left; ?>
		<div id="content">
		  <div class="page-header">
		    <div class="container-fluid">
		      <div class="pull-right">
		        <button type="submit" form="form-recent-products" data-toggle="tooltip" title="<?php echo $button_save; ?>" class="btn btn-primary"><i class="fa fa-save"></i></button>
		        <a href="<?php echo $cancel; ?>" data-toggle="tooltip" title="<?php echo $button_cancel; ?>" class="btn btn-default"><i class="fa fa-reply"></i></a></div>
		      <h1><?php echo $heading_title; ?></h1>
		      <ul class="breadcrumb">
		        <?php foreach ($breadcrumbs as $breadcrumb) { ?>
		        <li><a href="<?php echo $breadcrumb['href']; ?>"><?php echo $breadcrumb['text']; ?></a></li>
		        <?php } ?>
		      </ul>
		    </div>
		  </div>
		  <div class="container-fluid">
		    <?php if ($error_warning) { ?>
		    <div class="alert alert-danger"><i class="fa fa-exclamation-circle"></i> <?php echo $error_warning; ?>
		      <button type="button" class="close" data-dismiss="alert">&times;</button>
		    </div>
		    <?php } ?>
		    <div class="panel panel-default">
		      <div class="panel-heading">
		        <h3 class="panel-title"><i class="fa fa-pencil"></i> <?php echo $text_edit; ?></h3>
		      </div>
		      <div class="panel-body">
		        <form action="<?php echo $action; ?>" method="post" enctype="multipart/form-data" id="form-recent-products" class="form-horizontal">
		          <div class="form-group">
		            <label class="col-sm-2 control-label" for="input-name"><?php echo $entry_name; ?></label>
		            <div class="col-sm-10">
		              <input type="text" name="name" value="<?php echo $name; ?>" placeholder="<?php echo $entry_name; ?>" id="input-name" class="form-control" />
		              <?php if ($error_name) { ?>
		              <div class="text-danger"><?php echo $error_name; ?></div>
		              <?php } ?>
		            </div>
		          </div>
		          <div class="form-group">
		            <label class="col-sm-2 control-label" for="input-limit"><?php echo $entry_limit; ?></label>
		            <div class="col-sm-10">
		              <input type="text" name="limit" value="<?php echo $limit; ?>" placeholder="<?php echo $entry_limit; ?>" id="input-limit" class="form-control" />
		            </div>
		          </div>
		          <div class="form-group">
		            <label class="col-sm-2 control-label" for="input-status"><?php echo $entry_status; ?></label>
		            <div class="col-sm-10">
		              <select name="status" id="input-status" class="form-control">
		                <?php if ($status) { ?>
		                <option value="1" selected="selected"><?php echo $text_enabled; ?></option>
		                <option value="0"><?php echo $text_disabled; ?></option>
		                <?php } else { ?>
		                <option value="1"><?php echo $text_enabled; ?></option>
		                <option value="0" selected="selected"><?php echo $text_disabled; ?></option>
		                <?php } ?>
		              </select>
		            </div>
		          </div>
		        </form>
		      </div>
		    </div>
		  </div>
		</div>
		<?php echo $footer; ?>
	* Language file (optional) `admin/language/arabic/folder/module_name.php`
	  Use the following initial content
	  ```php
	  <?php
		// admin/language/english/module/recent_products.php
		// Heading
		$_['heading_title']    = 'Recent Products';

		// Text
		$_['text_module']      = 'Modules';
		$_['text_success']     = 'Success: You have modified Recent Products module!';
		$_['text_edit']        = 'Edit Recent Products Module';

		// Entry
		$_['entry_name']       = 'Module Name';
		$_['entry_limit']      = 'Limit';
		$_['entry_status']     = 'Status';

		// Error
		$_['error_permission'] = 'Warning: You do not have permission to modify Recent Products module!';
		$_['error_name']       = 'Module Name must be between 3 and 64 characters!';
	  ```
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
