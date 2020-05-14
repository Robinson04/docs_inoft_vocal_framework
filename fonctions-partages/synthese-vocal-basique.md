# Synthèse vocal basique

Pour générer la réponse à renvoyer à votre utilisateur, il est fréquent d'utiliser de la synthèse vocal basique. C'est à dire, où **vous avez besoin d'uniquement fournir un texte pour qu'il soit synthétisé en prenant la voix de la plateforme sur laquelle est l'utilisateur** \(si l'utilisateur est sur Alexa, votre texte sera synthétisé avec la voix d'Alexa. Sur Google Assistant, avec la voix que vous avez définis pour votre application, etc\).

Pour cela vous utiliserez la fonction `self.say()` pour répondre la même chose peu importe la plateforme, et 

{% hint style="warning" %}
Vous ne pouvez utiliser les fonctions des synthèses, uniquement si votre code est à l'intérieur d'une classe des classes pour créer des réponses dans votre application. [Voir toutes les classes](../classes-et-application/resume-de-toutes-les-classes.md)
{% endhint %}

## A savoir

### Additionner plusieurs textes

Si vous utiliser la fonction `self.say` plusieurs fois \(ou `self.say` cross-platform suivi de la même fonction spécifique à une plateforme comme `self.alexa.say`\). Vos différents messages seront mise bout à bout, à la place qu'uniquement le dernier message soit conservé.

## Synthèse cross-plateforme

#### **`self.say(text_or_ssml: str)`**

### Fonctionnement

Si vous voulez que votre application aille répondre vocalement à votre utilisateur avec le message sur toutes les plateformes, vous utiliserez la fonction **`self.say("Votre texte")`**

**La voix de synthèse qui sera utilisé seras celle de la plateforme actuel sur laquelle l'utilisateur interagit avec votre application**, et optionnellement pour Google Assistant la voix que vous avez choisi d'utiliser pour votre application dans la console de développement.

En utilisant cette synthèse basique, la génération de la synthèse vocal, ne se fait pas du côté de l'Inoft Vocal Framework. Le framework va uniquement envoyer à Alexa/Google Assistant/Bixby/etc... le texte à synthétiser, qui le sera de leurs coté. Contrairement par exemple au [module de montage audio dynamique](montage-audio-dynamique.md), cette technique ne créer aucun fichier audio que vous aurez besoin de stocker.

### Arguments

#### **text\_or\_ssml**

_Obligatoire \| Attends une valeur de type str \(texte\)_

Vous permet de spécifier le texte que vous voulez synthétiser. Vous pouvez également y passer un texte en format SSML [\(en apprendre plus sur le SSML et les réponses avancées\)](reponses-avancees-and-ssml/)

### Exemple

```python
self.say("Bien le bonjour l'ami !")
```

#### Résultat de l'exemple cross-platform sur le simulateur d'Alexa

{% embed url="https://www.youtube.com/watch?v=dvSuPmGreFw" %}

#### Résultat de l'exemple cross-platform sur le simulateur de Google Assistants

{% embed url="https://youtu.be/kVoSpFKYtS8" %}

### Usage

{% code title="app.py" %}
```python
from inoft_vocal_framework import InoftSkill, \
    InoftRequestHandler, InoftDefaultFallback

class LaunchRequestHandler(InoftRequestHandler):
    def can_handle(self) -> bool:
        return self.is_launch_request()

    def handle(self):
        self.say("Bien le bonjour l'ami !")
        return self.to_platform_dict()

# Une classe de DefaultFallback est obligatoire dans toutes applications
class DefaultFallback(InoftDefaultFallback):
    def handle(self):
        self.say("J'ai pas compris mon ami !")
        return self.to_platform_dict()

def lambda_handler(event, context):
    skill = InoftSkill()
    skill.add_request_handler(LaunchRequestHandler)
    skill.set_default_fallback_handler(DefaultFallback)
    return skill.handle_any_platform(event=event, context=context)
    
# Uniquement si le fichier est lancer depuis une machine local (et non sur
# le cloud). L'application sera lancé en simulant le lancement d'un skill Alexa.
if __name__ == "__main__":
    from inoft_vocal_framework import Simulator
    event_, context_ = Simulator(event_type="alexa/start").get_event_and_context()
    print(f"\n\nFinal Output : {lambda_handler(event=event_, context=context_)}")
```
{% endcode %}

_Pour déployer ce code et le lier à une application sur assistants vocaux, veuillez voir la page de déploiement ici_

## Synthèse par plateforme

### Fonctionnement

Si vous souhaitez avoir une réponse différente en fonction de la plateforme, vous pouvez accéder à la fonction `say` de manière spécifique à chaque plateforme à la place d'y accéder en cross-plateforme. A la place  d'y accéder via le classique `self.say`, accédez-y via `self.alexa.say`, `self.google.say`, ou `self.bixby.say`. Dès que vous accéder à une fonction via non pas directement via le terme self, mais via le terme self suivi du nom de la plateforme, soit vous utilisez une fonctionnalité spécifique à la plateforme, qui ne seras pas utiliser sur l'autre. Soit vous définissez un comportement spécifique pour votre application en fonction d'avec quel plateforme votre utilisateur utilise.

### Example

```python
self.alexa.say("Bienvenue dans le skill Alexa !")
self.google.say("Bienvenue dans l'action Google !")
```

#### Résultat de l'exemple spécifique à la plateforme sur le simulateur d'Alexa

{% embed url="https://youtu.be/hdHrJwiTK2k" %}

#### Résultat de l'exemple spécifique à la plateforme sur le simulateur de Google Assistant

{% embed url="https://youtu.be/uQ-hhtI177g" %}

### Usage

{% code title="app.py" %}
```python
from inoft_vocal_framework import InoftSkill, \
    InoftRequestHandler, InoftDefaultFallback

class LaunchRequestHandler(InoftRequestHandler):
    def can_handle(self) -> bool:
        return self.is_launch_request()

    def handle(self):
        self.alexa.say("Bienvenue dans le skill Alexa !")
        self.google.say("Bienvenue dans l'action Google !")
        return self.to_platform_dict()

# Une classe de DefaultFallback est obligatoire dans toutes applications
class DefaultFallback(InoftDefaultFallback):
    def handle(self):
        self.say("J'ai pas compris mon ami !")
        return self.to_platform_dict()

def lambda_handler(event, context):
    skill = InoftSkill()
    skill.add_request_handler(LaunchRequestHandler)
    skill.set_default_fallback_handler(DefaultFallback)
    return skill.handle_any_platform(event=event, context=context)
    
# Uniquement si le fichier est lancer depuis une machine local (et non sur
# le cloud). L'application sera lancé en simulant le lancement d'un skill Alexa.
if __name__ == "__main__":
    from inoft_vocal_framework import Simulator
    event_, context_ = Simulator(event_type="alexa/start").get_event_and_context()
    print(f"\n\nFinal Output : {lambda_handler(event=event_, context=context_)}")
```
{% endcode %}

_Pour déployer ce code et le lier à une application sur assistants vocaux, veuillez voir la page de déploiement ici_

