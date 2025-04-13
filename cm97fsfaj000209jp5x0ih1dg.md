---
title: "Top 9 Tips for Creating Outstanding APIs"
seoTitle: "Top 9 Tips for Creating Outstanding APIs – Best Practices for API Desi"
seoDescription: "Learn the top 9 tips for designing outstanding APIs that developers love. From following standards and proper versioning to using pagination and clear error"
datePublished: Sat Apr 05 2025 23:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm97fsfaj000209jp5x0ih1dg
slug: top-9-tips-for-creating-outstanding-apis
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/1FxMET2U5dU/upload/9f0faef343bf2565d4de95d1eeb2f8e6.jpeg
tags: api, java, python, apis, rest-api, api-design, api-basics, api-development

---

Some things never change, like how amazing it feels to use a beautifully designed API.  
I imagine it’s the same feeling Apple users always talk about🙄… it just works, intuitively, right out of the box.

Some of the APIs I’ve personally used that really capture this quality are:

* [GitHub API](https://docs.github.com/en/rest?apiVersion=2022-11-28) - Beautiful, well organized, loads of examples and status indicators, super stable.
    
* [Twilio API](https://www.twilio.com/docs/usage/api) - A toddler could use it with all the tutorials they have written for it👶.
    

On the other side of things, you can read [Casey’s blog](https://caseymuratori.com/blog_0025) to get a sense of the rabbit hole bad API design can drag you into.… Being on the receiving end of such an API is like trying to save burnt rice by drowning it in sauces to mask the flavour. You know you have little chance of success, but you try it anyway because you’re down bad and it’s your only option.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1744048911569/cf17c589-0337-46db-9503-aa85f7e1ffb1.png align="center")

Creating clear, concise, and consistent REST API endpoints is essential for usability, maintainability, and scalability. Here are some tips for creating RESTful API endpoints that make sense:

1. **Stick to Standards**
    
    * Use standard HTTP methods (`GET`, `POST`, `PUT`, `DELETE`, etc.) appropriately. For example, `GET` for retrieving data, `POST` for creating data, `PUT` or `PATCH` for updating data, and `DELETE` for removing data.
        
    * Make use of the Standard HTTP status codes to represent the status of the request. For instance, `200` for success, `400` for bad request, `404` for not found, `500` for server errors, etc.
        
2. **Use Nouns in URLs**
    
    * Name your resources using nouns, not verbs.
        
        * Good: `/users`
            
        * Bad: `/getUser`
            
    * If possible, use plural nouns (e.g., `/orders`, `/products`).
        
    * Sticking to this means your URLs should give an idea about the resource without being overly verbose.
        
3. **Nest Resources for Hierarchies**
    
    * If there's a relationship between resources, you can nest them.
        
        * Example: `/authors/123/books` to get books by a specific author.
            
    * Make sure you avoid Deep Nesting, which can be hard to read and might indicate your resources are too coupled. Limit nesting to one or two levels if possible.
        
    * See Below on when to use Nesting vs Filtering
        
4. **Use Query Parameters for Filtering**
    
    * If you want to provide options for filtering, sorting, or selecting specific fields, use query parameters.
        
        * Example: `/products?category=electronics&sort=price-asc`
            
            ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1744051281959/f30792d8-74c8-4f5b-8f29-ab72b6193d0b.png align="center")
            
5. **Version Your API**
    
    * Including a version number in your API endpoint can help maintain backward compatibility. Trust me, even if it feels unnecessary right now, it’s 100% worth it in the long run.
        
        * Example: `/v1/users`
            
6. **Provide Clear Error Messages**
    
    * When returning errors, provide a clear message, an error code, and, if applicable, a reference to the relevant documentation section.
        
        <div data-node-type="callout">
        <div data-node-type="callout-emoji">🚫</div>
        <div data-node-type="callout-text">The opposite of this? An API that throws the wrong error code for the issue you’re facing. Like returning a 400 when, in reality, the server is spiraling into a deranged crash loop and should definitely be hitting you with a 500.think</div>
        </div>
        
7. **Document Your API**
    
    * A well-documented API, possibly with tools like Swagger or a Postman collection that users can download, is invaluable. It helps users understand endpoints, request-response formats, error codes, etc. And the interactive element allows them to test this in real time swiftly
        
        <div data-node-type="callout">
        <div data-node-type="callout-emoji">💡</div>
        <div data-node-type="callout-text"><strong>TIP:</strong> A lot of frameworks can automatically generate Swagger docs or Postman collections for you, saving time and ensuring consistency.</div>
        </div>
        
8. **Support Pagination for Large Data Sets**
    
    * When returning large lists, please, for the love of all developers out there, use pagination to break up the results.
        
        * Example: `/users?page=2&limit=50`
            
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1744051251946/de61ebf7-6b9b-4ff6-b023-9479d75bf332.png align="center")
        
9. **Be Consistent**
    
    * Whatever conventions you decide to use, be consistent throughout your API. This makes it much easier for developers to predict endpoint names and functionality.
        

Remember, while it's important to stick to conventions, there's no one-size-fits-all approach to designing API endpoints. The needs and contexts of applications can differ widely. Tailor your API design to your application's specific requirements, while keeping these best practices in mind.

For more info on this topic I recommend ByteByteGo’s video

%[https://www.youtube.com/watch?v=_gQaygjm_hg&ab_channel=ByteByteGo] 

### When to use nesting vs filtering?

<div data-node-type="callout">
<div data-node-type="callout-emoji">❓</div>
<div data-node-type="callout-text">When should I use filtering and when should I use nesting of resources? e.g. I could create <code>GET /users?id=123</code> or <code>GET /users/123/</code></div>
</div>

Both filtering and nesting are common patterns in RESTful API design, and each has its place depending on the context. Here's when you might consider using each:

### Filtering:

Filtering is useful when:

1. **You're dealing with collections**: When you're retrieving a subset of a collection based on specific criteria, filtering is the way to go. For instance, you might want to get all orders made in the last week, or all users who joined after a certain date.
    
    Example:
    
    * `GET /orders?startDate=2023-01-01&endDate=2023-01-07`
        
    * `GET /users?joinedAfter=2023-01-01`
        
2. **Multiple criteria**: Filtering shines when you need to combine several criteria.
    
    Example:
    
    * `GET /products?category=electronics&priceBelow=100`
        
3. **Optional parameters**: Filtering works well when parameters can be optional. Using the above example, you might want to filter just by category or just by price.
    

### Nesting:

Nesting is useful when:

1. **You're expressing a hierarchy or ownership**: Nesting is often used to indicate a relationship between resources. It clearly denotes that one resource is a subset or owned by another.
    
    Example:
    
    * `GET /users/123/orders` - Get all orders for user 123.
        
    * `GET /books/456/chapters` - Get all chapters for book 456.
        
2. **Avoid ambiguity**: In some scenarios, you might want to avoid confusion between resource identifiers and filter parameters. Nesting can help here.
    
    Example:
    
    * Suppose `orders` can also be identified by alphanumeric strings like "special-order". In such a case, `GET /orders/special-order` could be mistaken for a filter if using the `?id=` pattern. But `GET /orders/special-order/items` is unambiguous.
        
3. **Resource-specific actions**: Sometimes, actions or methods are specific to a resource and make more sense when nested.
    
    Example:
    
    * `POST /users/123/reset-password` - An action specifically for user 123.
        

### Combining Both:

In many cases, it's actually quite sensible to combine both filtering and nesting to achieve more granular control.

Example:

* `GET /users/123/orders?status=shipped` - This nests to get orders for a specific user and then filters those orders to get only the ones that have been shipped.
    

### Bottom Line:

* Use **filtering** when dealing with a broad collection and you want to narrow down the results based on certain criteria.
    
* Use **nesting** when you want to denote hierarchy, ownership, or a clear relationship between resources.
    

In the case of your example (`GET /users?id=123` vs. `GET /users/123/`):

* `GET /users/123` is more conventional for retrieving a specific resource by its identifier.
    
* `GET /users?id=123` is a filter on the `users` collection, but in this specific use case (retrieving by ID), it's more conventional and clear to use the nested format.
    

Here are five examples for both scenarios:

### When to use Nesting:

1. **Author and Their Books**
    
    * To fetch all books written by a specific author: `GET /authors/123/books` This indicates a clear hierarchical relationship between an author and their books.
        
2. **User and Their Orders**
    
    * To retrieve all orders made by a particular user: `GET /users/456/orders` This indicates that the orders belong to or are associated with a specific user.
        
3. **Blog and Its Comments**
    
    * To fetch comments for a specific blog post: `GET /blogs/789/comments` The nested structure communicates that you're interested in the comments associated with a specific blog post.
        

### When to use Filtering:

1. **Search Products by Category**
    
    * To fetch products in the "electronics" category: `GET /products?category=electronics` Instead of nesting, filtering provides a flexible way to query products based on various attributes.
        
2. **Filter Users by Registration Date**
    
    * To get users who registered after a certain date: `GET /users?registeredAfter=2023-01-01` Filtering is ideal here because registration date isn't a hierarchical or ownership relationship.
        
3. **Search for Articles Containing Keywords**
    
    * To retrieve articles containing the keyword "AI": `GET /articles?keyword=AI` Keywords can vary, and filtering allows querying based on dynamic content.
        

In summary, nesting is particularly useful when there's a clear hierarchical or ownership relationship, while filtering is ideal for narrowing down collections based on varying criteria.