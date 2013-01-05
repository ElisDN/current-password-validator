Current Password Validator
===
Validates field "Input current password" in Yii model.

Readme
---

- [README RUS](http://www.elisdn.ru/blog/10/dcurrentpassword-validaciia-tekushego-parolia)
- [Extension](http://www.yiiframework.com/extension/current-password-validator/)

Installation
------------

Extract to `protected/components`.

Usage example
-------------

***Simple***

~~~
[php]
class User extends CActiveRecord
{
	public $old_password;
	public $new_password;
 
	// ...  
	public function rules()
	{
		return array(	   
			// ...		  
			// Settings
			array('old_password', 'required', 'on' => 'settings'),
			array('old_password', 'DCurrentPassword', 'on' => 'settings'), 
		));
	}
	 
	public function validatePassword($password)
	{
		return $this->hashPassword($password) === $this->password;
	}
}
~~~

***Advanced***

~~~
[php]
class UserForm extends CFormModel
{
	public $id // storage for User primary key value 
	 
	// User model fields
	public $login;
	public $password;
	public $email;
	// ...
	 
	// Empty fields for new values
	public $old_password;
	public $new_password;
	public $new_confirm;
	public $new_email;
	 
	public function rules()
	{
		return array(
		 
			// ...
			 
			array('old_password, new_email, new_password, new_confirm', 'length', 'max'=>255),
			array('new_email', 'email'),		 
			array('new_password', 'length', 'min'=>6, 'allowEmpty'=>true),
			array('new_confirm', 'compare', 'compareAttribute'=>'new_password'),
			 
			array('old_password', 'DCurrentPassword', 
				'on'=>'settings', 
				'className'=>'User', 
				'dependsOnAttributes'=>array('new_password', 'new_email'), 
				'emptyMessage'=>'Current password required for change email or password',
				'notValidMessage'=>'Current password is not correct',
			),
		));
	}
	
	// ...
}
~~~

~~~
[php]
protected function actionSettings()
{
	$user = $this->loadUserModel();
	$user->scenario = 'settings'; 
	 
	$form = new UserForm(); 
	$form->scenario = 'settings'; 
	 
	// Store primary key value in `id` variable
	$form->id = $user->getPrimaryKey();	
	 
	if (isset($_POST['UserForm'])
	{   
		$form->attributes = $_POST['UserForm'];
		if ($form->validate())
		{	   
			$user->attributes = $_POST['UserForm'];
			if ($user->save())
			{
				Yii::app()->user->setFlash('settings-form', 'Success!');
				$this->refresh();
			}
		}
	}
	$this->render('settings', array('model'=>$form);
}
~~~