# GIT SNIPPET

### Log changes:
```
git log --oneline
```

### Show all branch:
```
git branch
```

### Change branch name

   + Step 1: Rename branch in local:
```
git branch -m bugfix
```
   + Step 2: Delete branch in remote:
    
```
git push origin --delete bogfix
```

### Add change to committed
```
 git commit --amend -a
```

### Clear change in local branch
```
git checkout --file
git checkout -- .
```

### Remove untrack file
```
git clean -f -d
```

### Remove file in staging (committed)
```
git reset --file
```

### Remove commit
```
git reset --hard HEAD~~
```

### Resolve conflict
- Commit file change

- Pull code

- Resolve conflict

- Commit resolve conflict

- Push code
```
git commit -a -m "Message"
git pull
git add .
git commit 
git push
```

### Using git credential to store config
```
git config --global credential.helper "store"
```
https://stackoverflow.com/questions/45925964/how-to-use-git-credential-store-on-wsl-ubuntu-on-windows

More about git: https://backlog.com/git-tutorial/vn/stepup/stepup1_1.html

# DRUPAL
### Get fields in view
```
{{ row.content['#row']._entity.title.value }}
{{ view.style_plugin.getField(key, 'title') }}
```

 ### Get field in view field:
```
 fields._fieldsname.content
```

### Import/export db using drush
---export 
```
vendor/bin/drush sql-dump > a.sql
```
---import
```
vendor/bin/drush sql-cli < 20221114.sql
```

### Get params
If you have the url for example /page?uid=123&num=452

To get all params, use: 

$param = \Drupal::request()->query->all();

To get "uid" from the url, use:

$uid = \Drupal::request()->query->get('uid');

To get "num" from the url, use:

$num = \Drupal::request()->query->get('num');

### enable debug:
```
$config['system.logging']['error_level'] = 'verbose';
```

### Get paging info in query database
```
    $query = \Drupal::entityQuery('node');
    $query->condition('status', 1);
    $query->condition('type', 'n_trips');
    $query->pager(4);
    $count_query = clone $query;

    $items = $query->execute();

    $count = $count_query->count()->execute();
    /** @var \Drupal\Core\Pager\PagerManagerInterface $pager */
    $pager = \Drupal::service('pager.manager');
    $paging = $pager->createPager($count, 4);
    dd($items, $count, $paging->getLimit());
```
    
### Get translate node
```
  foreach ($greenChoicesNodes as $greenChoicesNode) {
    if ($greenChoicesNode->hasTranslation($langcode)) {
      $translation = $greenChoicesNode->getTranslation($langcode);
      $title = $translation->field_display_title->value;
      $options[$greenChoicesNode->id()] = $title;
    }
  }
```


### Join one table with multiple table
```
$or = new Condition('OR');
$or->condition("node__field_n_p_banner.field_n_p_banner_target_id", NULL, 'IS NOT NULL');
$or->condition("taxonomy_term__field_carousel.field_carousel_target_id", NULL, 'IS NOT NULL');
$this->query->addWhere($this->options['group'], $or);
```


### Custom formState Paragraph

```
/**
 * Implements hook_field_widget_WIDGET_TYPE_form_alter().
 *
 * Add conditional fields in banner  paragraphs.
 */
function cbt_utilities_field_widget_paragraphs_form_alter(&$element, \Drupal\Core\Form\FormStateInterface $form_state, $context) {
  /** @var \Drupal\field\Entity\FieldConfig $field_definition */
  $field_definition = $context['items']->getFieldDefinition();
  $paragraph_entity_reference_field_name = $field_definition->getName();

  if ($paragraph_entity_reference_field_name == 'field_n_p_banner') {
    /** @see \Drupal\paragraphs\Plugin\Field\FieldWidget\ParagraphsWidget::formElement() */
    $widget_state = \Drupal\Core\Field\WidgetBase::getWidgetState($element['#field_parents'], $paragraph_entity_reference_field_name, $form_state);

    /** @var \Drupal\paragraphs\Entity\Paragraph $paragraph */
    $paragraph_instance = $widget_state['paragraphs'][$element['#delta']]['entity'];
    $paragraph_type = $paragraph_instance->bundle();
    // Determine which paragraph type is being embedded.
    if ($paragraph_type == 'p_banner') {
      $element['subform']['field_p_title']['widget'][0]['value']['#states'] = [
        'required' => [
          ':input[name="field_n_p_highlight_on_banner[value]"]' => ['checked' => TRUE],
        ]
      ];
      $element['subform']['field_p_short_description']['widget'][0]['value']['#states'] = [
        'required' => [
          ':input[name="field_n_p_highlight_on_banner[value]"]' => ['checked' => TRUE],
        ]
      ];
    }
  }
}

```
