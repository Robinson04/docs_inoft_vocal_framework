# Create a Request \(InoftRequestHandler\)

Pour gérer une requête de l'utilisateur, le framework dispose de la classe InoftRequestHandler. Une classe gérant le lancement de l'application se présenterait de cette façon :

```
from inoft_vocal_framework import InoftRequestHandler

class StartRequestHandler(InoftRequestHandler):
    def can_handle(self):
        return self.is_launch_request()
        
    def handle(self):
        self.say("Bien le bonjour cher ami !")
        return self.to_plateform_dict()
```

Dans notre example, nous créons une nouvelle class s'appelant StartRequestHandler \(voir les conventions pour nommer les classes\), qui hérite de la class InoftRequestHandler \(que nous avons importé dans la première ligne de code\). Puis nous définissons la logique des deux fonctions obligatoires d'une classe InoftRequestHandler :

### La fonction can\_handle :

L'Inoft Vocal Framework fonctionne avec série de classes. Dans votre application, vous ajoutez toutes les classes que vous avez créer \(comme par exemple notre classe StartRequestHandler\), à chaque interaction qu'à un utilisateur avec votre application, uniquement une de ces classes seras utilisé pour répondre à l'utilisateur et exécutez votre logique.

La fonction can\_handle définis si votre 





Les classes InoftRequestHandler

{% hint style="warning" %}
Si plusieurs classes ont leurs fonctions can\_handle pouvait toutes deux s'activant en même temps, 
{% endhint %}

On

```text
from inoft_vocal_framework import InoftSkill, InoftRequestHandler, InoftDefaultFallback

class StartRequestHandler(InoftRequestHandler):
    def can_handle(self):
        return self.is_launch_request()
        
    def handle(self):
        self.say("Bien le bonjour cher ami !")
        self.persistent_memorize("hasLaunchedAtLeastOnce", True)
        return self.to_plateform_dict()
       
class SecondTimeLaunchHandler(InoftRequestHandler):
    def can_handle(self):
        if (self.is_launch_request() and
            self.persistent_remember("hasLaunchedAtLeastOnce", bool) is True):
            return True
        
    def handle(self):
        self.say("Hey l'ami ! Tu as lancer l'application une deuxième fois !")
        return self.to_plateform_dict()

class DefaultFallback(InoftDefaultFallback):
    def handle(self):
        self.say("Je n'ai pas compris mon ami !")
        return self.to_platform_dict()

def lambda_handler(event, context):
   skill = InoftSkill()
   skill.add_request_handler(StartRequestHandler)
   skill.add_request_handler(SecondTimeLaunchHandler)
   skill.set_default_fallback_handler(DefaultFallback)
   return skill.handle_any_platform(event=event, context=context)
```

{% hint style="danger" %}
Même lors du deuxième lancement de l'application, l'utilisateur ne recevrais que "Bien le bonjour cher ami"
{% endhint %}

ce you're strong enough, save the world:

{% code title="hello.sh" %}
```bash
# Ain't no code for that yet, sorry
echo 'You got to trust me on this, I saved the world'
```
{% endcode %}



