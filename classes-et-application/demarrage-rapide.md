# Démarrage rapide

Pour chaque application, vous devez créer un objet InoftSkill. Vous y ajouterez toutes vos classes \([Toutes les classes](../classes.md)\). Puis vous utiliserer la function "handle\_any\_platform" pour gérer la requête de l'utilisateur, et lui renvoyer une réponse.

```text
def lambda_handler(event, context):
    skill = InoftSkill()
    skill.add_request_handler(StartRequestHandler)
    skill.add_state_handler(SayYesStateHandler)
    skill.set_default_fallback_handler(DefaultFallback)
    return skill.handle_any_platform(event=event, context=context)
```

{% hint style="danger" %}
Si vous ne définissez pas de DefaultFallbackHandler, une erreur se produira au lancement de l'application, vous indiquant que vous devez en définir une.
{% endhint %}

