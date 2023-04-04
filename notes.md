# Optimizely CMS Notes
## Content 

There are several different base types or categories of content in Optimizely CMS can be 

1. Pages
2. Blocks 
3. Media Files
4. Folders

(Other products, like Optimizely Customized Commerce, have their own base types of content.)

Content Model, Content type inherits from the Content Interface and contains a set of specific properties.


![Alt text](https://files.readme.io/4b1f714-content-2.png)

(IContent) --> interface
IContent interface also provides propertied such as 
##
a. Display Name

b. References

c. Unique Identifiersf

# Content Type :- IContentData -> Base class
### Content, content types, and content templates are linked together in the following way:

* A content type defines a set of properties, for example, name and publish date. 
* A specific implementation can create several content types from the different content base types.
* Content types are usually defined in code as .NET classes inheriting from IContentData
* There are different content base types in CMS such as
## 
    a. Pages (Base Class) -: EPiServer.Core.PageData
    b. Blocks (Base Class) -: EpiServer.core.BlockData
    c. Media (Its Interface) -: EPiServer.Core.IContentMedia

    Note:- So to add a content type to CMS, add a .NET class to the project, inheriting the appropriate base class or interface (for pages for example PageData) and decorate the class with ContentTypeAttribute. 

# Page :- 
### Create a Page content Type:- 
    * To add properties that we want to have for all page types (base type - PageData)
    * Added an abstract SitePageData base class which inherits from EpiServer.Core.PageData.
    * Base class has an SEO property which we want used on all pages inheriting from the class.

### Example:- A simple article page type with a "MainBody" editor area of the property type XhtmlString type, inheriting from the SitePageData base class.



    using System;
    using System.ComponentModel.DataAnnotations;
    using EPiServer.Core;
    using EPiServer.DataAbstraction;
    using EPiServer.DataAnnotations;
    using EPiServer.SpecializedProperties;
    
    namespace MyOptimizelySite.Models.Pages
    {
      [ContentType(DisplayName = "ArticlePage", GUID = "b8fe8485-587d-4880-b485-a52430ea55de", Description = "Basic page type for creating articles.")]
      public class ArticlePage : SitePageData
      {
        [CultureSpecific]
        [Display(
                  Name = "Main body",
                  Description = "The main body editor area lets you insert text and images into a page.",
                  GroupName = SystemTabNames.Content,
                  Order = 10)]
        public virtual XhtmlString MainBody { get; set; }
             
      }
    }


### Example: The SitePage base class, with the SEO property.

    namespace MyOptimizelySite.Models.Pages
    {
      public abstract class SitePageData : EPiServer.Core.PageData
      {
        [Display(GroupName = "SEO", Order = 200, Name = "Search keywords")]
        public virtual String MetaKeywords { get; set; }
      }
    }

# Note:- 
    * A recommendation is to assign a unique GUID for the ContentTypeAttribute for all types. (this makes it possible to rename or move the class later)
    * Content types implicitly contain a set of built-in properties which are available for all content instance, regardless of the content type instance.
[For Content Meta Data Properties Click Here!](https://docs.developers.optimizely.com/content-cloud/v12.0.0-content-cloud/docs/content-metadata-properties)

## Note :- Why are the properties declared as virtual here?
    1. What happens in the background is that a proxy class is created for the page type.
    2. The proxy will override the properties with an implementation, and this only works if properties are declared as virtual.
    3. If the properties are not declared virtual, you need to implement get/set so that these will read/write data to the underlying property collection instead.

## Custom content base type :- 

 * You can define a custom content type that is not based on any existing base type.
 * A custom-defined content type that does not belong to a known base type.

 [Click Here for More Information!](https://docs.developers.optimizely.com/content-cloud/v12.0.0-content-cloud/docs/persisting-icontent-instances)


### _When we create a new content type, its based on an existing base type :-_ 
                        
                        * PageData
                        * BlockData
                        * IContentMedia etc ... 


# Blocks :- EPiServer.Core.BlockData 
* Used as reusable smaller content parts which can be added to pages.
* Blocks or list of blocks can also be used in code as an instance or list property on a content model (example :- A Page)
* Blocks, block types, and block templates are linked together.
* Blocks can only be rendered in the context of other content, such as a page.
* A block instance is either part of a content instance or a shared instance.
* ***The BlockData object is the programmatic representation of a block, containing the properties defined in your .NET class.***
* ***The value of currentBlock is automatically set to the BlockData object that is requested by the client.***

        Content Instance :- 
           a. A content instance is an instance of the .NET class defining the content type.
           b. When a content instance is created in edit view, values are assigned to the properties and stored in the database.

        Shared Instance :-
           a. the blocks are stored, loaded, and versioned individually as an own entity, and can be referenced from multiple pages or blocks.     
## _Note :-_     
* A block type defines a set of properties, for example, a heading and a page listing.
* A blockis an instance of the .NET class defining the block type.
* Associated view components and views are used to render the block in some context.

## Blocks Types :- 
* Block types are usually defined in code as classes based on a model inheriting from _**EPiServer.Core.BlockData**_.
* During initialization, the assemblies are scanned for .NET classes inheriting _**BlockData**_ and decorated with _**ContentTypeAttribute**_.

## Creating a block type :- 
###  A simple block type with properties for a heading and a link to an image. TeaserBlock inherits from BlockData, which inherits from EPiServer.Core.BlockData.

    using System;
    using System.ComponentModel.DataAnnotations;
    using EPiServer.Core;
    using EPiServer.DataAbstraction;
    using EPiServer.DataAnnotations;
    using EPiServer.Web;
    
    namespace MyOptimizelySite.Models.Blocks
    {
      [ContentType(DisplayName = "TeaserBlock", 
                   GUID = "38d57768-e09e-4da9-90df-54c73c61b270", 
                   Description = "Heading and image.")]
      public class TeaserBlock : BlockData
      {
        [CultureSpecific]
        [Display(Name = "Heading",
                 Description = "Add a heading.",
                 GroupName = SystemTabNames.Content,
                 Order = 1)]
        public virtual String Heading { get; set; }
    
        [Display(Name = "Image", Description = "Add an image (optional)", 
                 GroupName = SystemTabNames.Content,
                 Order = 2)]
        public virtual ContentReference Image { get; set; } 
      }
    }

## Steps to be noted while creating blocks :-
* It is recommended to assign a unique GUID for the block type to support future renaming.
* Blocks will only be editable from the All Properties edit view.
* Only be previewed in context of some other content like a page. 



[For more Brief Information about Content Types Information Click Here](content_type.md)    
    



### Content Template :- 
## 
* Fetches the stored property values and renders the output.
* It can be associated with the content type, to render the output in a sertain context.

