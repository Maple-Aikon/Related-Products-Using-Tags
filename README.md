Purpose: Display a random set of products that contain the same tag(s) as the currently viewed product.

jQuery is necessary for this to function. 

Shopify, liquid and basic jQuery knowledge is also required for installation.

###1.) Download and include the jQuery product randomizer from Caroline Schnapp and upload it into your store assets folder. 
[https://github.com/carolineschnapp/jquery-pick](https://github.com/carolineschnapp/jquery-pick)

###2.) Include the following code inside your theme.liquid file.
```
{{ 'jquery.pick.js' | asset_url | script_tag }}   

<script>
  jQuery(function() {
      jQuery('#related_tags .related_prd').pick(5);
  });
</script>
```

###3.) Update the value inside the .pick() method. 
The value set is the number of related tagged products that will display.

###4.) Create a related_tags snippet and include the following code.
```
<div id="related_tags" style="display: none">

    {% capture currentProduct %}{{ product.title }}{% endcapture %}

    {% for tag in product.tags %}

        {% include 'rt_prd_gen' with 'prd_tag_x' %}
        {% include 'rt_prd_gen' with 'prd_tag_y' %}

    {% endfor %}

</div>

<script>
  
  $(document).ready(function() {
      $('#related_tags').css('display', 'initial');
  });
  
</script>
```
###5.) Replace the prd_tag_x / prd_tag_y with a tag of your choice.

Example: 

{% include 'rt_prd_gen' with 'funny_hat' %} 

{% include 'rt_prd_gen' with 'serious_hat' %}

The script inside the snippet changes the display 'none' to 'initial' after the page is loaded. This is done so the user doesn't see all products being loaded and then reduced. The currentProduct capture will be used later to check if any of the related tagged products are the same as the currently viewed product.

###6.) Create a rt_prd_gen snippet and include the following code.
```
{% capture currentPrdTag %}{{ rt_prd_gen }}{% endcapture %}

{% if tag == currentPrdTag %}
    {% for product in collections.all.products %}     
        {% for tag in product.tags %}
            {% if tag == currentPrdTag %}

                {% if product.title != currentProduct %}         
                            
                    <div class="related_prd">
                      <a href="{{ product.url }}">
                        <img src="{{ product.featured_image | product_img_url: "medium" }}"><br>
                        {{ product.title }}<br>
                        {{ product.price | money }}
                      </a>
                    </div>

                {% else %}

                {% endif %}     

            {% endif%}
        {% endfor %}
    {% endfor %}
{% endif %}
```
This code checks if the **prd_tag_x** tag is on our currently viewed product, and then it looks through all of the products and their tags in the store to see if any of them have a matching tag.

If it finds a matching tag on a product, it adds that product to a list. If it runs into a product that matches the one being currently viewed, it simply ignores it to avoid duplication. Once thats done, the matching tagged products will get radomized and the value placed in the .pick() method will be the amount of products to display on the page.

###Final Step: Include the following code in your product.liquid file and watch the magic happen.
```
{% include 'related_tags' %}
```

###NOTES

Randomizer source

https://docs.shopify.com/manual/configuration/store-customization/showcasing-products/can-i-feature-randomly-picked-products
