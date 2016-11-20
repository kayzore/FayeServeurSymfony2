Faye Serveur avec Symfony2
=======

Exemple de site Symfony2 permettant la mise en place d'un système de Chat/Notification en temps réel avec Node.JS et Faye.
(Serveur Faye Node.JS) : [FayeServeur Node.JS](https://github.com/kayzore/FayeServeurNodeJS))

## Installation

- Obtenir [FayeServeur Node.JS](https://github.com/kayzore/FayeServeurNodeJS) et effectuer l'installation.
- Sur Symfony2 :
    - Installation de la libraire via composer
    ```composer.json
    {
        "require": {
             "nc/faye-client": "v1.1.0"
        }
    }
    ```
    - Définition d’un service client (dans Acme/DemoBundle/Resources/config/services.yml par exemple)
    ```routing.yml
    parameters:
        acme_demo.faye.endpoint:      'http://localhost:3000/'
        acme_demo.faye.adapter.class: 'Nc\FayeClient\Adapter\CurlAdapter'
        acme_demo.faye.client.class:  'Nc\FayeClient\Client'
    
    services:
        acme_demo.faye.adapter:
            class:     %acme_demo.faye.adapter.class%
        acme_demo.faye.client:
            class:     %acme_demo.faye.client.class%
            arguments:
                - @acme_demo.faye.adapter
                - %acme_demo.faye.endpoint%
    ```    

## Usage

- Dans un controlleur :
```php
// récupération du client
$faye = $this->container->get('acme_demo.faye.client');

// construction d'un message
$channel = '/messages';
$data    = array('text' => 'Lorem ipsum dolor sin amet...');

// envoi du message
$faye->send($channel, $data);
```
- Dans une page :
```javascript
// Connection du client au serveur Faye (Node.JS)
var client = new Faye.Client('http://localhost:3000/');

// Inscription du client au channel "notifications" & lorsqu'on recoit un message affichage dans la console
client.subscribe('/notifications', function (message) {
    console.log(message.text);
});

// Envoi d'un message sur le channel "notifications"
var message = 'Hello World !';
var sendNotification = client.publish('/notifications', {text: message});
sendNotification.then(function() {
    console.log('Message send.');
}, function(error) {
  console.log('ERROR : ' + error.message);
});
```