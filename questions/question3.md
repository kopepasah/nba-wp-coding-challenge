### Consider the following scenario:

#### For all players in the NBA so far:

* A `player` custom post type exists with a meta field called `player_external_id`
  * Assume all players have a unique value in this field (different than the WordPress post ID)
  
#### For the upcoming season, all players will have a page on a 3rd party site with the following URL structure:

`http://www.nba-player-tv.com/channel/{player_external_id}`

* A new meta field `player_tv_url` was added to the `player` custom post
* All new players drafted for the upcoming season have the proper value set

#### Summary

| Type | Name |
| ---- | ---- |
| Custom Post Type | player |
| Custom Fields | player_external_id, player_tv_url |

### We need to update any missing `player_tv_url` post metadata

1. Write the code that would accomplish this.
1. How would you trigger the execution of this code?


---

# Answer

First, I would ask why you need to populate an additional meta field for this, as the URL is always the player ID (which aready exists). So, when outputting this information somewhere, we could simply output as such:

```php
<?php echo esc_url( 'http://www.nba-player-tv.com/channel/' . $player_external_id ); ?>
```

However, if it was required that this new meta field exist, we would need to query the players which do not have this set and set it using WP-CLI.

```php
// Args to query all players who do not have this field set, using WP_Query.
$args = [
    'post_type'      => 'player'
    'posts_per_page' => -1,
    'meta_query'     => [
        'key'     => 'player_tv_url',
        'compare' => 'NOT EXISTS',
    ],
];
```

From here, we would simply loop over the returned players and update the field using `update_post_meta()`.

```php
$player_external_id = get_post_meta( $player->ID, 'player_external_id', true );

update_post_meta( $player->ID, 'player_tv_url', esc_url( 'http://www.nba-player-tv.com/channel/' . $player_external_id ) );
```

---

All of this would be done using WP-CLI.

Happy to write this in a more "real world" sceniero way, if needed.
