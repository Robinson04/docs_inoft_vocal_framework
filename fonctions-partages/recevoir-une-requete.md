# Recevoir une requête

Comme expliqué dans [la page de l'activation par invocation](../concepts-de-base/activation-par-invocation.md), votre application vas recevoir une requête \(comme lancement\) à laquelle votre application vas fournir une réponse \(comme "Bonjour, comment puis-je vous aider ?"\). Puis tant que vous ne spécifiez pas à votre application de terminer l'interaction avec votre utilisateur, une réponse et donc nouvelle requête vas être attendu de la part de l'utilisateur, pour que vous lui fournissiez à nouveau une réponse.

Les réponses



## Gérer le lancement de l'application

#### **`self.is_launch_request()`**

### Fonctionnement

Cette fonction retourneras une valeur `True` si la requête de l'utilisateur est le lancement classique de votre application, et `False` si ce n'est pas le cas. Vous utiliserez principalement cela dans la fonction `can_handle` d'une classe `InoftRequestHandler`.

{% hint style="warning" %}
Si un utilisateur lance votre application en incluant directement une intention - par exemple, à la place de dire "Alexa, lance le jeu" qu'il dise "Alexa, demande au jeu de reprendre ma partie" -, alors votre application ne recevras pas de requête de lancement, mais une requête d'intention.
{% endhint %}

Pour rappel, une classe de type InoftRequestHandler à deux fonctions obligatoires \(et d'autres optionnelles\). La fonction `handle`  qui contient votre logique pour construire une réponse pour votre utilisateur. Et la fonction `can_handle` qui si retourne la valeur `True` va informer l'application que la fonction `handle` de la même classe devrait être utiliser pour créer la réponse que l'utilisateur vas recevoir, et que les potentiels suivantes classes pouvant fournir une réponse n'ont plus besoin d'être utiliser pour le moment \(jusqu'à la prochaine requête de l'utilisateur, où tout ce processus vas se répéter\).

### Exemple

```python
def can_handle(self) -> bool:
    return self.is_launch_request()
```

### Usage

{% code title="app.py" %}
```python
from inoft_vocal_framework import InoftSkill, \
    InoftRequestHandler, InoftDefaultFallback

class LaunchRequestHandler(InoftRequestHandler):
    def can_handle(self) -> bool:
        return self.is_launch_request()

    def handle(self):
        self.say("Bien le bonjour et l'aurevoir l'ami !")
        self.end_session()
        return self.to_platform_dict()

# Une classe de DefaultFallback est obligatoire dans toutes applications
class DefaultFallback(InoftDefaultFallback):
    def handle(self):
        self.say("J'ai pas compris mon ami ! Excellente journée à toi !")
        self.end_session()
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
    event_, context_ = Simulator(platform=Simulator.PLATFORM_ALEXA,
                                 event_type="launch").get_event_and_context()
    print(f"\n\nFinal Output : {lambda_handler(event=event_, context=context_)}")
```
{% endcode %}

_Pour déployer ce code et le lier à une application sur assistants vocaux, veuillez voir la page de déploiement ici_

## Gérer l'intention d'un utilisateur

#### **`self.is_in_intent_names(intent_names_list: list or str)`**

### Fonctionnement

Cette fonction retourneras une valeur `True` si la requête de l'utilisateur est le lancement classique de votre application, et `False` si ce n'est pas le cas. Vous utiliserez cette fonction `can_handle` d'une classe `InoftRequestHandler` et également dans la logique de vos fonctions `handle` pour vos états à choix multiples.

Pour rappel, une classe de type InoftRequestHandler à deux fonctions obligatoires \(et d'autres optionnelles\). La fonction `handle`  qui contient votre logique pour construire une réponse pour votre utilisateur. Et la fonction `can_handle` qui si retourne la valeur `True` va informer l'application que la fonction `handle` de la même classe devrait être utiliser pour créer la réponse que l'utilisateur vas recevoir, et que les potentiels suivantes classes pouvant fournir une réponse n'ont plus besoin d'être utiliser pour le moment \(jusqu'à la prochaine requête de l'utilisateur, où tout ce processus vas se répéter\).

### Utilisation indépendante dans un InoftRequestHandler

Contrairement aux choix multiples \(tant que votre utilisateur n'est pas dans un choix multiple\) les InoftRequestHandler peuvent être déclenchée à n'importe quel moment. Si vous souhaitez créer une application à parcours non-linéaire où les utilisateurs peuvent accéder 

{% hint style="warning" %}
Si un utilisateur lance votre application en incluant directement une intention - par exemple, à la place de dire "Alexa, lance le jeu" qu'il dise "Alexa, demande au jeu de reprendre ma partie" -, alors votre application ne recevras pas de requête de lancement, mais une requête d'intention.
{% endhint %}

### Utilisation dans état à choix multiples \(InoftStateHandler\)

### Exemples

#### A l'intérieur de la fonction can\_handle d'une classe InoftRequestHandler

```python
class WantCookiesRequestHandler(InoftRequestHandler):
    def can_handle(self) -> bool:
        return self.is_in_intent_names(["WantCookies"])
```

#### A l'intérieur de la fonction handle d'un état à choix multiples \(une classe InoftStateHandler\)

```python
class WantCookiesStateHandler(InoftStateHandler):
    def handle(self):
        if self.is_in_intent_names(["Oui", "AMAZON.YesIntent"]):
            self.say("Je t'en donnerais pas ! Mouahhahahah ! Aller salut !")
            self.end_session()
            return self.to_platform_dict()
```

### Usage

{% code title="app.py" %}
```python
from inoft_vocal_framework import InoftSkill, \
    InoftRequestHandler, InoftStateHandler, InoftDefaultFallback

class LaunchRequestHandler(InoftRequestHandler):
    def can_handle(self) -> bool:
        return self.is_launch_request()

    def handle(self):
        self.say("Bien le bonjour l'ami ! Tu veut des cookies ?")
        self.memorize_session_then_state(WantCookiesStateHandler)
        return self.to_platform_dict()    

class WantCookiesStateHandler(InoftStateHandler):
    def handle(self):
        if self.is_in_intent_names(["Oui", "AMAZON.YesIntent"]):
            self.say("Je t'en donnerais pas ! Mouahhahahah ! Aller salut !")
            self.end_session()
            return self.to_platform_dict()

        elif self.is_in_intent_names(["Non", "AMAZON.NoIntent"]):
            self.say("De toute façon j'allais rien te donner !"
                     "Vas manger des carrotes, aller salut !")
            self.end_session() 
            return self.to_platform_dict()

    def fallback(self):
        self.say("Tu veux des cookie, oui ou non ?")
        return self.to_platform_dict()

# Une classe de DefaultFallback est obligatoire dans toutes applications
class DefaultFallback(InoftDefaultFallback):
    def handle(self):
        self.say("J'ai pas compris mon ami ! Tu veut des cookies ?")
        self.memorize_session_then_state(WantCookiesStateHandler)
        return self.to_platform_dict()

def lambda_handler(event, context):
    skill = InoftSkill()
    skill.add_request_handler(LaunchRequestHandler)
    skill.add_state_handler(WantCookiesStateHandler)
    skill.set_default_fallback_handler(DefaultFallback)
    return skill.handle_any_platform(event=event, context=context)
    
# Uniquement si le fichier est lancer depuis une machine local (et non sur
# le cloud). L'application sera lancé en simulant le lancement d'un skill Alexa.
if __name__ == "__main__":
    from inoft_vocal_framework import Simulator
    event_, context_ = Simulator(platform=Simulator.PLATFORM_ALEXA,
                                 event_type="launch").get_event_and_context()
    print(f"\n\nFinal Output : {lambda_handler(event=event_, context=context_)}")
```
{% endcode %}

_Pour déployer ce code et le lier à une application sur assistants vocaux, veuillez voir la page de déploiement ici_

### Usage

{% code title="app.py" %}
```python
from inoft_vocal_framework import InoftSkill, \
    InoftRequestHandler, InoftStateHandler, InoftDefaultFallback

class LaunchRequestHandler(InoftRequestHandler):
    def can_handle(self) -> bool:
        return self.is_launch_request()

    def handle(self):
        self.say("Bienvenue dans la FAQ de l'Inoft Vocal Framework ! "
                 "Pose moi une question comme 'Quand est-ce que le framework "
                 "à été créer ?', si j'en connais la réponse, je te répondrais. "
                 "Alors l'ami ? Je t'écoute.")
        return self.to_platform_dict()    

class CreationDateRequestHandler(InoftRequestHandler):
    def can_handle(self) -> bool:
        return self.is_in_intent_names("FrameworkCreationDate")

    def handle(self):
        self.say("Le premier commit du framework sur GitHub était le 1er "
                 "février 2020. Et pourtant, le travail avait été commencé "
                 "plus d'une semaine plus tôt. Et encore, les premiers "
                 "utilitaires pour les assistants vocaux que Robinson "
                 "a créer, et qui ont menée à la création de l'Inoft "
                 "Vocal Framework, datent de Septembre 2019 ! "
                 "J'attend ta prochaine question l'ami !")
        return self.to_platform_dict()
                 
 class FrameworkLicenseRequestHandler(InoftRequestHandler):
    def can_handle(self) -> bool:
        return self.is_in_intent_names("FrameworkLicense")

    def handle(self):
        self.say("Le framework est sous la license MIT. Ca te donne "
                 "une liberté total sur ce que tu fais avec. Tu peut "
                 "l'utiliser pour des projets commerciaux sans avoir "
                 "besoin de le mentionner, tu peut le modifier, en faire "
                 "ta propre version, ou même vendre la version existante "
                 "sans même la modiifer. Bref, tu est totalement libre ! "
                 "J'attend ta prochaine question l'ami !")
        return self.to_platform_dict()
                 
 class SexiestManRequestHandler(InoftRequestHandler):
     def can_handle(self) -> bool:
         return self.is_in_intent_names("SexiestMan")
         
     def handle(self):
         self.say("Ta question est un peu hors sujet ! On est là pour"
                  "parler du framework bon sang ! Mais bon, si tu veut "
                  "savoir, c'est une évidence, c'est Mads Mikkelsen ! "
                  "J'attend ta prochaine question l'ami !")
          return self.to_platform_dict()

# Une classe de DefaultFallback est obligatoire dans toutes applications
class DefaultFallback(InoftDefaultFallback):
    def handle(self):
        self.say("Aaaah... Tu m'a eu, malheuresement je n'ai pas "
                 "la réponse à cette question. Pose-en moi une autre, "
                 "je suis prêt à parié que j'aurais la réponse !")
        return self.to_platform_dict()

def lambda_handler(event, context):
    skill = InoftSkill()
    skill.add_request_handler(LaunchRequestHandler)
    skill.add_request_handler(CreationDateRequestHandler)
    skill.add_request_handler(FrameworkLicenseRequestHandler)
    skill.add_request_handler(SexiestManRequestHandler)
    skill.set_default_fallback_handler(DefaultFallback)
    return skill.handle_any_platform(event=event, context=context)
    
# Uniquement si le fichier est lancer depuis une machine local (et non sur
# le cloud). L'application sera lancé en simulant le lancement d'un skill Alexa.
if __name__ == "__main__":
    from inoft_vocal_framework import Simulator
    event_, context_ = Simulator(platform=Simulator.PLATFORM_ALEXA,
                                 event_type="launch").get_event_and_context()
    print(f"\n\nFinal Output : {lambda_handler(event=event_, context=context_)}")
```
{% endcode %}

_Pour déployer ce code et le lier à une application sur assistants vocaux, veuillez voir la page de déploiement ici_

