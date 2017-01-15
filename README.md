# yii2-paystack
YII 2 component for paystack payment integration

[![Latest Stable Version](https://poser.pugx.org/smladeoye/yii2-paystack/v/stable.png)](https://packagist.org/packages/smladeoye/yii2-paystack)
[![Total Downloads](https://poser.pugx.org/smladeoye/yii2-paystack/downloads.png)](https://packagist.org/packages/smladeoye/yii2-paystack)

- [Installation] (https://github.com/smladeoye/yii2-paystack#installation)
- [Configuration] (https://github.com/smladeoye/yii2-paystack#configuration)
- [Usage] (https://github.com/smladeoye/yii2-paystack#usage-example)
- [Event Handling for Paystack Operations] (https://github.com/smladeoye/yii2-paystack#handling-events)
- [Paystack Inline Widget] (https://github.com/smladeoye/yii2-paystack#using-the-paystack-inline-payment-widget)


## Installation

The preferred way to install this extension is through composer.

Either run

```bash
composer require smladeoye/yii2-paystack
```

**OR**

add '"smladeoye/yii2-paystack": "1.0.0"' to the require section of your composer.json file, then run:

```bash
composer install
```

## Configuration

In your configuration file (web.php) register the component with the necessary configurations, for example:

```php
'components'=>[
    //  ...
    'Paystack' => [
        'class' => 'smladeoye\paystack\Paystack',
    	'environment' => 'test',
    	'testPublicKey'=>'pk_test_365589cc6b0e95f1fb53fa0eeaef6b1819f1b0f2',
    	'testSecretKey'=>'sk_test_1a4a88eaec6f4f3b23771edb2c60fe8d8b95cbe',
    	'livePublicKey'=>'',
    	'liveSecretKey'=>'',
    ],
    //  ...
]
```

## Usage Example
```php

// Initializing a payment transaction

$paystack = Yii::$app->Paystack;

$transaction = $paystack->transaction();
$transaction->initialize(['email'=>'smladeoye@gmail.com','amount'=>'100000','currency'=>'NGN']);

// check if an error occured during the operation
if (!$transaction->hasError)
{
    // response property for response gotten for any operation
    $response = $transaction->getResponse()

    // redirect the user to the payment page gotten from the initialization
    $transaction->redirect();
}
else
{
    // display message
    echo $transaction->message;

    // get all the errors information regarding the operation from paystack
    $error = $transaction->getError();
}

```

There are seven operations available that can be performed which have been grouped based on Paystack's own grouping.
Each of the operations also have their individual methods that can be called for performing different actions
(create, list -- fetchAll, fetch, update,...) which can accept all the necessary parameters as an array.

The following are the available operations and methods (all sample codes are based on the demo configuration above):


1. **customer**:   To initiatiate any customer operation:

  ```php
  $paystack = Yii::$app->Paystack;
  $customer = $paystack->customer();
  ```
  Distinct methods available to customer:

  - **whitelist** --> whitelist a particular customer.Example:

  ```php
        $customer->whitelist($customer_id);
  ```
  + **blacklist** --> blacklist a particular customer.Example:

  ```php
        $customer->blacklist($customer_id);
  ```

2. **transaction**:    To initiate a transaction operation:

  ```php
  $paystack = Yii::$app->Paystack;
  $transaction = $paystack->transaction();
  ```
  Distinct methods available to transaction:

  - **initialize** --> initialize a transaction; an authorization url is generated from this method after which the redirect method can then be called to redirect to the payment page. Example:

  ```php
  $transaction->initialize(['email'=>'smladeoye@gmail.com','amount'=>'10000']);
  if (!$transaction->hasError)
        $transaction->redirect();
  ```
  + **verify** --> verify a transaction.Example:

  ```php
        $transaction->verify($trans_reference);
  ```

  + **charge** --> charge authorization for recurring transactions.Example:

  ```php
  $transaction->charge($options = []);
  ```

  + **timeline** --> timeline for a particular transactions.Example:

  ```php
  $transaction->timeline($trx_id);
  ```

  + **total** --> get total for transactions within a specified range.Example:

  ```php
   $transaction->total($from,$to);
   //An array could be provided instead with the available parameters in key => value format.
  ```

  + **export** --> export a range of transaction details;a url is generated from this method from which the
    file can be downloaded. To get the path simpley call the path method or call the download method to download the file. Example:

  ```php
     $transaction->export($options = []);

    //get download link url
    $transaction->getExportUrl();
  ```

  OR to download the file, call:

  ```php
        $transaction->download();
  ```

3. **subscription**:    To initiate a subscription operation:

  ```php
  $paystack = Yii::$app->Paystack;
  $subscription = $paystack->subscription();
  ```
  Distinct methods available to subscription:

  + **enable** --> enable a customer subscription.Example:

  ```php
      $subscription->enable($code, $token);
      //an array can be provided instead, containing the necessary parameters as key => value
  ```


  + **disable** --> disable a customer subscription.Example:

  ```php
      $subscription->disable($code, $token);
      //an array can be provided instead, containing the necessary parameters as key => value
  ```

4. **subaccount**:    To initiate a subaccount operation:

  ```php
  $paystack = Yii::$app->Paystack;
  $subaccount = $paystack->subaccount();
  ```
  Distinct methods available to subaccount:

  - **listBank** --> list the available bank for creating subaccounts on the system.Example:

  ```php
          $subaccount->enable($code, $token);
          //an array can be provided instead, containing the necessary parameters as key => value
  ```

5. **plan**:    To initiate a plan operation:

  ```php
  $paystack = Yii::$app->Paystack;
  $plan = $paystack->plan();
  ```
  Plan operation contains all the basic methods (create,fetch,fetchAll,update);

6. **page**:    To initiate a page operation:
  ```php
  $paystack = Yii::$app->Paystack;
  $page = $paystack->page();
  ```
  Distinct methods available to page:

  - **checkAvailability** --> check the availability of a particular slug.Example:

  ```php
          $page->checkAvailability($slud_id);
  ```

7.  **settlement**:    To initiate a settlement operation:
  ```php
  $paystack = Yii::$app->Paystack;
  $settlement = $paystack->settlement();
  ```
  Distinct methods available to settlement:

  - **fetchAll** --> fetch all settlements.Example:

  ```php
  $settlement->fetchAll($from_date,$to_date);
  //an array can be provided instead, containing the necessary parameters as key => value
  ```

#### The follwing methods are also available:

+ **fetchAll**: The fetchall (list) method is available for all operations.Example:

```php
        $customer->fetchAll(['page'=>'','perPage'=>'']);
```

+ **create**: The create method is available for customer, subscription, subaccount, page and plan operations.Example:

```php
        $customer->create(['email'=>'smladeoye@gmail.com']);
```

+ **fetch**   --> The fetch method is available to all operations except settlement.Example:

```php
        $customer->fetch($customer_id);
```

+ **update**  --> The update method is available for customer, subaccount, page and plan operations.Example:

```php
        $customer->update($id,$info = array();
```
### Handling Events
The component also supports event handling before and after every request. You can read more on
YII2 EVENTS [HERE] (http://www.yiiframework.com/doc-2.0/guide-concept-events.html).
You can set the event handler by configuring the property beforeSend - (event before sending
the request) and the afterSend - (event after sending the request).
The event handlers can be set for all operations or for specific operations; event handlers set
for specific operations overwrites the one set for all operations.
Example:

```php
'Paystack' => [
            'class' => 'smladeoye\paystack\Paystack',
            ...//other configurations just like example above

            //setting the event handler for all operations before any request is made
            'beforeSend'=>'var_dump',

            //will set the event handler for all operations after any request is made
            'afterSend'=>'var_dump',

            //setting the event handler for the transaction operation; this will overwrite the event handlers above
            'transaction'=>array(
                //handler for the event before any request is made for a transaction operation
                'beforeSend'=>'var_dump',

                //handler for the event after any request is made for a transaction operation
                'afterSend'=>'var_dump',
            )
        ]
```
### Using the Paystack Inline Payment Widget

To use the widget, call the widget from your view and set the widget parameters, example:

```php
use smladeoye\paystack\widget\PaystackWidget;

    echo PaystackWidget::widget(
        [
        //set the text to be displayed on the button
            'buttonText'=>'PAY',
        //array to set other button attributes like id, class,style etc
            'buttonOptions'=>array(
                'class'=>'btn btn-danger',
                'style'=>'width: 80px;',
            ),
        //array to set all necessary paystack inline payment options
            'options'=>[
                //your paystack public key
                'key'=>Yii::$app->Paystack->testPublicKey,
                'email'=>'smladeoye@ymail.com',
                'ref'=>'123456789',
                'amount'=>'200000',
                'currency' =>'NGN',
                'plan' =>'my-plan',
                'quantity' =>'2',
                //callbackUrl can be set, where the tansaction reference would be passed as GET parameter
                'callbackUrl' =>'www.google.com',
                //also u can override the default with the callback option, simply provide javascript anonymous function as a string
                //'callback'=>'function(response){alert(response.trxref);};',
            ],
        ]
    );

```
