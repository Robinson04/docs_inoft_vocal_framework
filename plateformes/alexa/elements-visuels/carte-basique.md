# Carte Basique

**self.show\_basic\_card\(self, title: str, text: str, small\_image\_url: Optional\[str\] = None, large\_image\_url: Optional\[str\] = None\) -&gt; None:**

## Fonctionnement

## **Example**

```python
self.say("Bonjour !")
self.alexa.show_basic_card(title="Voila Robinson !", text="En toute splendeur",
    large_image_url="https://s3.eu-west-3.amazonaws.com/www.inoft.com/img/team_robinson_128x128.jpg")
```

{% hint style="warning" %}
Si en essayant le code ci-dessus, une erreur se produit ou que l'image n'apparaît pas, c'est tout simplement car le lien de l'image est mort. Essayer avec un lien vers une autre image \(n'importe lequel que vous trouverez sur internet, à condition qu'elle respecte les conditions d'hébergements d'images pour Alexa\)
{% endhint %}

![Carte avec titre, texte et image sur un t&#xE9;l&#xE9;phone avec l&apos;application mobile Alexa](../../../.gitbook/assets/screenshot_20200507_194547_com.amazon.dee.app.jpg)

![M&#xEA;me carte mais sur simulateur d&apos;appareil avec une taille d&apos;&#xE9;cran moyenne \(Echo Show\)](../../../.gitbook/assets/doc3.jpg)

![M&#xEA;me carte, mais sur simulateur d&apos;un appareil avec une petite taille d&apos;&#xE9;cran \(Echo Spot\)](../../../.gitbook/assets/docs4.jpg)

## **Usage**

{% code title="app.py" %}
```python
from inoft_vocal_framework import InoftSkill, \
    InoftRequestHandler, InoftDefaultFallback

class LaunchRequestHandler(InoftRequestHandler):
    def can_handle(self) -> bool:
        return self.is_launch_request()

    def handle(self):
        self.say("Bonjour !")
        self.alexa.show_basic_card(title="Voila Robinson !",
            text="En toute splendeur",
            large_image_url="https://s3.eu-west-3.amazonaws.com/www.inoft.com/img/team_robinson_128x128.jpg"
        )
        # Si une erreur se produis ou que l'image n'apparait pas,
        # cela veut dire que le lien de l'image ne fonctionne plus.
        # Utiliser le lien de n'importe quel autre image (tant qu'elle
        # respecte les conditions d'hébergement d'images d'Alexa)
        return self.to_platform_dict()

class DefaultFallback(InoftDefaultFallback):
    def handle(self):
        self.say("J'ai pas compris mon ami !")
        return self.to_platform_dict()

def lambda_handler(event, context):
    skill = InoftSkill()
    skill.add_request_handler(LaunchRequestHandler)
    skill.set_default_fallback_handler(DefaultFallback)
    return skill.handle_any_platform(event=event, context=context)
```
{% endcode %}

