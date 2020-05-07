---
description: >-
  InoftSkill(settings_yaml_filepath: Optional[str] = None,
  settings_json_filepath: Optional[str] = None):
---

# InoftSkill \(application\)

### Fonctionnement

L'InoftSkill est l'objet qui ramène ensemble toutes vos classes \(RequestHandler, StateHandler, etc\), qui vas utiliser le fichier de configuration de votre application \(voir fichier de configuration\), et recevoir les requêtes de vos utilisateurs et générez la réponse adéquate.

Si vous n'avez pas spécifié d'emplacement précis de votre fichier de configuration, l'InoftSkill vas chercher dans le dossier où se trouve votre code Python, un fichier nommé app\_settings.yaml  \(voir fichier de configuration\)

{% hint style="info" %}
Si vous avez plusieurs fichiers dans multiples dossier, la recherche de votre fichier de configuration vas partir du fichier de code où vous avez initialiser l'objet InoftSkill
{% endhint %}

### Arguments:

**settings\_yaml\_filepath:**

Vous permet de spécifier directement le fichier de configuration \(en format .yaml\) de votre application. Il n'est pas recommandé de l'utiliser, mais vous en aurez besoin si vous voulez que votre fichier de configuration ne soit pas dans le même dossier que votre principale fichier de code \(celui où l'objet InoftSkill est initialisé\)

Example :

```text
from inoft_vocal_framework import InoftSkill
import os

skill = InoftSkill(settings_yaml_filepath=os.path.join(
    os.path.dirname(os.path.abspath(__file__)),
    # Trouve le chemin d'accès du dossier dans lequel se trouve
    # votre fichier de code qui est en train d'être éxécuter
    "dossier_pratique",
    # Ajoute au chemin d'accès, le dossier nommé dossier_pratique
    "app_settings.yaml")
    # Puis le nom de votre fichier de configuration
)
```

**settings\_json\_filepath:**

Exactement pareil que l'argument settings\_yaml\_filepath, mais à utiliser si votre fichier de configuration est en format .json et non .yaml

{% hint style="danger" %}
N'utiliser pas de chemin d'accès direct \(comme C:/Users/Rob/application/app\_settings.yaml\) Cela fonctionnera sur votre machine, cependant ne fonctionneras pas une fois déployé dans le cloud, étant donné que les chemins d'accès seront différents. Pour que cela fonctionne sur votre machine et dans le cloud, basé sur vous sur le chemin d'accès de votre fichier de code principale que vous pouvez avoir avec : os.path.dirname\(os.path.abspath\(\_\_file\_\_\)\)
{% endhint %}





### Usage

```text
# Nom de fichier : app.py

from inoft_vocal_framework import InoftSkill, \
    InoftRequestHandler, InoftStateHandler, InoftDefaultFallback

class LaunchRequestHandler(InoftRequestHandler):
    def can_handle(self) -> bool:
        return self.is_launch_request()

    def handle(self):
        self.say("Bien le bonjour ! Est-ce que vas bien mon ami ?")
        self.memorize_session_then_state(LaunchStateHandler)
        return self.to_platform_dict()

class LaunchStateHandler(InoftStateHandler):
    def handle(self):
        if self.is_in_intent_names(["Yes"]):
            self.say("Ca me fait plaisir ! Je te souhaite la meilleur des journées !")
        elif self.is_in_intent_names(["No"])
            self.say("Mince, ne t'inquiète pas, je te garanti que ta journée vas être excellente ! ")
        else:
            self.say("Je n'ai pas très bien compris. Peut-être une autre fois ?")    
        
        # Peu importe la réponse, la session se fini ici.
        self.end_session()
        return self.to_plateform_dict()

class DefaultFallback(InoftDefaultFallback):
    def handle(self):
        self.say("J'ai pas compris mon ami !")
        return self.to_platform_dict()

def lambda_handler(event, context):
    # L'objet InoftSkill doit obligatoirement être initier
    # et paramétré dans la function lambda_handler
    
    skill = InoftSkill()
    # Pas de chemin d'accès du fichier de configuration à été spécifié. Le fichier
    # app_settings.yaml vas être chercher dans le dossier contenant ce fichier Python.
    
    skill.add_request_handler(LaunchRequestHandler)
    skill.add_state_handler(LaunchStateHandler)
    skill.set_default_fallback_handler(DefaultFallback)
    # Les RequestHandler, StateHandler et DefaultFallback sont ajouté un par un.
    # PS : Si aucun DefaultFallback n'est spécifié, une erreur se produiras au lancement.
    
    return skill.handle_any_platform(event=event, context=context)
    # L'InoftSkill vas traiter l'event (la requête de l'utilisateur)
    # et retourner son résultat (la réponse de l'application) comme résultat.
```

