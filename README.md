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

More info: https://drupal.bermaki.com/drupal-documentation-and-guides
https://gorannikolovski.com/snippets?page=1
https://github.com/flashvnn/drupal-snippets/blob/master/README.md
### Get fields in view
```
{{ row.content['#row']._entity.title.value }}
{{ view.style_plugin.getField(key, 'title') }}
```

 ### Get field in view field:
```
 fields._fieldsname.content
```
module for twig field: https://www.drupal.org/project/twig_field_value

### Import/export db using drush
- Export 
```
vendor/bin/drush sql-dump > a.sql
```
- Import
```
vendor/bin/drush sql-cli < 20221114.sql
```

### Get params in request
** If you have the url for example** /page?uid=123&num=452

** To get all params, use: **
```
$param = \Drupal::request()->query->all();
```
** To get "uid" from the url, use:**
```
$uid = \Drupal::request()->query->get('uid');
```
** To get "num" from the url, use:**
```
$num = \Drupal::request()->query->get('num');
```
### Enable debug:
```
$config['system.logging']['error_level'] = 'verbose';
```

### Get pager in query database
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
     ->extend('Drupal\Core\Database\Query\PagerSelectExtender')
      ->limit(self::ITEMS_PER_PAGE)
```
```
public static function getPager($totalItems, $limit): array
  {
    /** @var \Drupal\Core\Pager\PagerManagerInterface $pager */
    $pager = \Drupal::service('pager.manager');
    $paging = $pager->createPager($totalItems, $limit);
    return [
      'count' => $paging->getTotalItems(),
      'pages' => $paging->getTotalPages(),
      'items_per_page' => $paging->getLimit(),
      'current_page' => $paging->getCurrentPage(),
      'next_page' => $paging->getCurrentPage() + 1 >= $paging->getTotalPages() ? 0 : $paging->getCurrentPage() + 1
    ];
  }
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

### Render image with image style
```
use Drupal\image\Entity\ImageStyle;

$thumbUri = 'uri link;
$imgStyleThumb = ImageStyle::load('40_x_40')
   ->buildUrl($thumbUri);
$thumbnailImage = \Drupal::service('file_url_generator')
   ->generateAbsoluteString($imgStyleThumb);

```
### Using multiple Path Auto patterm for entity
```
use Drupal\pathauto\PathautoPatternInterface;

function mymodule_pathauto_pattern_alter(PathautoPatternInterface $pattern, array $context) {
  if ($context['module'] == 'node' && $context['op'] === "update" && $context['bundle'] === "news") {
    $node = $context['data']['node'];
    $archived = $node->get('field_archived')->getString() === "1";
    if ($archived) {
      $pattern->setPattern('/archive/[node::title]');
    }
  }
}
```
### Create Drush command batch
```
class TripPathAliasByDestinationCommands extends DrushCommands {

  /**
   * Update trip path alias by destination.
   *
   * @command cmd:update_trip_path_alias_by_destination
   * @aliases cmd:utpabd
   *
   * @usage cmd:update_region_trip_path
   *   Batch for update trip path alias by destination.
   */
  public function update($destinationId) {
    $this->logger()->notice("Start Batch Update Trip Path Alias.");
      ...

    if (empty($trips)) {
      return;
    }
    // Chunk data
    $trips = array_chunk($trips, 50);
    $operations = [];
    foreach ($trips as $splitTrips) {
      $operations[] = [
        '\Drupal\utilities\Commands\TripPathAliasByDestinationCommands::processUpdatePathAlias',
        [
          $splitTrips,
        ],
      ];
    }

    $batch = [
      'title' => t('Executing @num trip(s)', ['@num' => count($trips)]),
      'operations' => $operations,
      'finished' => '\Drupal\utilities\Commands\TripPathAliasByDestinationCommands::finishUpdatePathAlias',
    ];

    batch_set($batch);
    drush_backend_batch_process();
    $this->logger()->notice("Finish Batch Update Trip Path Alias.");
  }

  /**
   * processUpdatePathAlias.
   */
  public static function processUpdatePathAlias($trips, &$context) {
    $context['results'][] = count($trips);
    foreach ($trips as $trip) {
      try {
       ...
      }
      catch (\Exception $ex) {
        \Drupal::logger('update_trip_path_alias_by_destination')
          ->error("Can Not Update Trip Path ($trip), Error: {$ex->getMessage()}.");
      }
    }
  }

  /**
   * finishUpdatePathAlias.
   */
  public static function finishUpdatePathAlias($success, array $results, array $operations) {
    if ($success) {
      $count = array_sum($results);
      \Drupal::logger('update_trip_path_alias_by_destination')->info(t('Finish Batch Update Trip Path Alias. @count results processed.', [
        '@count' => $count,
      ]));
    }
    else {
      $errorOperation = reset($operations);
      \Drupal::logger('update_trip_path_alias_by_destination')->info(t('Finish Batch Update Trip Path Alias. An error occurred while processing @operation with arguments : @args.', [
        '@operation' => $errorOperation[0],
        '@args' => print_r($errorOperation[0], TRUE),
      ]));
    }
  }

}
```
* drush.services.yml
```
  cmd_update_trip_path_alias_by_destination.commands:
    class: \Drupal\utilities\Commands\TripPathAliasByDestinationCommands
    tags:
      - { name: drush.command }
  ```

### Generate Path Alias
   Checkbox auto generate
```
 $entity = \Drupal::entityTypeManager()->getStorage('node')->load($id);
 $entity->set('path', ['pathauto' => PathautoState::CREATE]);
 $entity->save();
```
   Create manual path
```
 $entity = \Drupal::entityTypeManager()->getStorage('node')->load($id);
 $entity->set('path', [
  'alias' => '/mypath/' . $title,
  'pathauto' => PathautoState::SKIP,
 ]);
 $entity->save();
```
