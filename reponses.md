# Réponses

* self.say
* 
## Répondre via voix synthétique

Pour répondre à votre utilisateur, vous utiliserez la commande "self.say". Elle fonctionne sur toutes les plateformes, et vas utiliser la voix synthétique de la plateforme utilisé \(elle auras la voix d'Alexa sur Alexa, mais la voix des applications Google Assistant sur Google\).

```
self.say("Bien le bonjour !")
```

{% hint style="danger" %}
Vous ne pouvez utiliser la commande self.say uniquement à l'intérieur d'une des classe de l'Inoft Vocal Framework : [Toutes les classes](classes.md) 
{% endhint %}

### Répondre différemment par plateforme

Vous pouvez soit employer une approche manuel :

```text
if self.is_alexa_v1:
    self.say("Bienvenue dans le skill Alexa !")
elif self.is_dialogflow_v1:
    self.say("Bienvenue dans l'action Google !")
```

Ou vous pouvez également avoir le même résultat, de la manière suivante :

```text
self.alexa.say("Bienvenue dans le skill Alexa !")
self.google.say("Bienvenue dans l'action Google !")
```

Sous son capo, la seconde méthode est implémentée de la même manière que la première. Nous recommandons fortement d'utiliser la seconde méthode.

