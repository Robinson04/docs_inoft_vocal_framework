# Noms de classes

Tous les conseils situés plus bas ne sont en aucun cas des nécessités techniques, uniquement les conventions de l'Inoft Vocal Framework



#### 1 - Quel est un bon terme pour l'activation de votre classe ?

Pour une classe gérant le lancement, ce qui la déclenche est le démarrage ou le lancement de l'application, donc "Start" ou "Launch" est un bon terme représentant la classe.

#### 2 - Ajouter le type de classe que vous utiliser

Si votre classe est un InoftRequestHandler, ajouter-y à votre terme RequestHandler, si c'est une classe InoftStateHandler, ajouter-y StateHandler, etc...

#### 3 - Pour les choix multiples \(InoftStateHandler\), décrivez leurs logique : 

Pour vous y retrouver plus facilement quand vous les appellerez, essayer de décrire dans le nom ce que fait votre choix multiple. Par exemple, si votre choix vas dire un fait à l'utilisateur si il répond "Oui", nous pourrions le synthétiser en "SayFactOnYes"

#### 4 - Suivez les conventions de Python

Principalement, de garder le noms de classes courts, et que chaque mot dans le nom de votre classe commence par une majuscule.

#### Voici toutes les conventions sur les noms de classes en actions :

```bash
class YesCondition(InoftCondition):
    def can_handle(self) -> bool:
        return self.is_in_intent_names(["AMAZON.YesIntent", "YesIntent"])

class StartRequestHandler(InoftRequestHandler):
    def can_handle(self) -> bool:
        return self.is_launch_request()

    def handle(self) -> dict:
        self.say("Est-tu prend pour éteindre ton premier fait ?")
        self.memorize_session_then_state(SayFactOnYesStateHandler)
        return self.to_platform_dict()

class GetFactRequestHandler(InoftRequestHandler):
    def can_handle(self) -> bool:
        return self.is_in_intent_names("GetFact")

    def handle(self) -> dict:
        from messages import MSGS_FACTS
        self.say(f"{MSGS_FACTS.pick()} Veut-tu un autre fait de fou ?")
        self.memorize_session_then_state(SayFactOnYesStateHandler)
        return self.to_platform_dict()

class SayFactOnYesStateHandler(InoftStateHandler):
    def handle(self) -> dict:
        if YesCondition(self).can_handle():
            return GetFactRequestHandler(self).handle()
        else:
            self.say("Tu ne veut pas de fait ? MON DIEU !! AUREVOIR !")
            self.end_session()
            return self.to_platform_dict()
```

{% hint style="danger" %}
 En Python le nom d'une classe ne peut pas contenir d'espaces, de caractères spéciaux, ou commencer par un chiffre.
{% endhint %}



