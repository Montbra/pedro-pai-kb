
Para redirecionar para uma outra página da sua aplicação com um button use a tag `<a>` (anchor) com classes de button e o método `url()` do Laravel conforme exemplo abaixo:

```html
<a class="br-button secondary mr-3" href="{{ url('/') }}">
	Cancelar <i class="fas fa-stop"></i> 
</a>
```

O link gerado terá aparência de button devido as classes e será capaz de redirecionar para a url fornecida.


>[!info]
> ### Why should you use url() method ?
>The reason is simple, the url method will get the full url to your controller. If you don't use this, href link will get appended with current url.
For example, suppose your button is located inside a given page:
> `yourdomain.com/a-given-page/` 
When someone clicks in your button, the result will be:
>`yourdomain.com/a-given-page/problems/{problem-id}/edit`
When you would like to get this:
>`yourdomain.com/problems/{problem-id}/edit`

Referência:
	https://stackoverflow.com/questions/38709886/call-route-from-button-click-laravel