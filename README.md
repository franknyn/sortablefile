better sortablefile
============

In addition to what [sortablefile module](https://github.com/bummzack/sortablefile) can do, this plugin provides a way of customising file edit fields much more flexible than [UploadFiled->setFileEditFields()](https://docs.silverstripe.org/en/3.4/developer_guides/forms/field_types/uploadfield/#edit-uploaded-images) . You can edit many_many_extraFields for file. 

Example setup for many_many_extraFields
-------------


```php
class SlideShow extends DataObject
{
    private static $db = array(
        'Name' => 'Text'
    );

    private static $many_many = array(
        'Images' => 'Image'
    );

    private static $many_many_extraFields = array(
        'Images' => array (
            'SortOrder' => 'Int', 
            'Link' => 'Text'
        )
    );

    // Use this in your templates to get the correctly sorted images
    // OR use $Images.Sort('SortOrder') in your templates which 
    // will unclutter your PHP classes
    public function SortedImages(){
        return $this->Images()->Sort('SortOrder');
    }

    function getCMSFields() {
        $fields = parent::getCMSFields(); 
        $fields->removeFieldFromTab('Root', 'Images');

        $fields->addFieldToTab(
            'Root.Main',
            $uploadField=SortableUploadField::create('Images', 'Upload slideshow images here and sort them by dragging their thumbnails')
            ->addExtraClass('longlabel')
            ->setFolderName('slideshow')
            ->setAllowedFileCategories('image')
        );

        //add many many extra link field
        $uploadField->updateFileEditFields(function($fileFields) {
            $fileFields->removeByName('OwnerID');
            $fileFields->removeByName('ParentID');
            $fileFields->removeByName('Title');
            $fileFields->removeByName('Name');
            $fileFields->insertBefore('',TextareaField::create("ManyMany[Link]", "Absolute Link"));
        });

        return $fields;            
    }
}
```

    public function getCMSFields()
    {
        $fields = parent::getCMSFields();
    
        // Use SortableUploadField instead of UploadField!
        $imageField = new SortableUploadField('Images', 'Portfolio images');
    
        $fields->addFieldToTab('Root.Images', $imageField);
        return $fields;
    }
}
```