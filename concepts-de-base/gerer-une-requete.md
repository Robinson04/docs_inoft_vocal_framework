# Gérer une requete

Pour gérer une requête de l'utilisateur, le framework dispose de la classe InoftRequestHandler

```
from inoft_vocal_framework import InoftRequestHandler

StartRequestHandler(InoftRequestHandler):
    def can_handle(self):
        return self.is_launch_request()
        
    def handle(self):
        self.say("Bien le bonjour cher ami !")
        return self.to_plateform_dict()
```

{% hint style="info" %}
 Super-powers are granted randomly so please submit an issue if you're not happy with yours.
{% endhint %}

Once you're strong enough, save the world:

{% code title="hello.sh" %}
```bash
# Ain't no code for that yet, sorry
echo 'You got to trust me on this, I saved the world'
```
{% endcode %}



