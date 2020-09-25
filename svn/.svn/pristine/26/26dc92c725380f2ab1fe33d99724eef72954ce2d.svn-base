<?php
/*
   Plugin Name: Feedback Fish
   Plugin URI: https://feedback.fish
   description: >-
  Collect issues, ideas and compliments for your website with a beautiful widget.
   Version: 1.0
   Author: Feedback Fish
   Author URI: http://feedback.fish
   License: MIT
   */

// Inject the feedback.fish JavaScript file into the DOM just before the body
function enqueue_feedback_fish_script()
{
    $projectId = get_option('feedback_fish_project_id');
    if (!empty($projectId)) {
        wp_register_script(
            'feedback-fish',
            "https://feedback.fish/ff.js?pid=$projectId",
            [],
            null,
            true
        );
        wp_enqueue_script('feedback-fish');
    }
}
add_action('wp_enqueue_scripts', 'enqueue_feedback_fish_script');

// Add defer="defer" to the feedback.fish <script> tag to avoid blocking render
// Taken from: https://wordpress.stackexchange.com/a/38335
add_filter(
    'script_loader_tag',
    function ($tag, $handle) {
        if ('feedback-fish' !== $handle) {
            return $tag;
        }

        return str_replace(' src', ' defer="defer" src', $tag);
    },
    10,
    2
);

// Add "Send feedback" to the primary nav if it exists
function add_feedback_fish_nav_menu_item($items, $args)
{
    $current_user = wp_get_current_user();
    $assigned_menu_slug = get_option('feedback_fish_selected_menu', 0);

    if (
        !empty($assigned_menu_slug) &&
        $args->menu->slug === $assigned_menu_slug
    ) {
        $items .= "<li class=\"menu-item\"><a href=\"#\" data-feedback-fish-userid=\"$current_user->user_email\" data-feedback-fish>Send feedback</a></li>";
    }
    return $items;
}
add_filter('wp_nav_menu_items', 'add_feedback_fish_nav_menu_item', 10, 2);

// Add "Feedback Fish" settings page to the WPAdmin menu
function create_feedback_fish_settings_page()
{
    $page_title = 'Feedback Fish Settings';
    $menu_title = 'Feedback Fish';
    $capability = 'manage_options';
    $slug = 'feedback_fish';
    $callback = 'feedback_fish_settings_page_content';

    add_submenu_page(
        'plugins.php',
        $page_title,
        $menu_title,
        $capability,
        $slug,
        $callback
    );
}
add_action('admin_menu', 'create_feedback_fish_settings_page');

// General content for the settings page
function feedback_fish_settings_page_content()
{
    ?>
  <div class="wrap">
    <h2>Feedback Fish</h2>
    <form method="post" action="options.php">
      <?php
      settings_fields('feedback_fish');
      do_settings_sections('feedback_fish');
      submit_button();?>
    </form>
  </div>
<?php
}

// Setup the settings page sections
add_action('admin_init', 'setup_feedback_fish_settings_page_sections');
add_action('admin_init', 'setup_feeback_fish_settings_fields');
function setup_feedback_fish_settings_page_sections()
{
    add_settings_section(
        'feedback_fish_general',
        'General',
        'feedback_fish_settings_page_section_callback',
        'feedback_fish'
    );
}

function setup_feeback_fish_settings_fields()
{
    add_settings_field(
        'feedback_fish_project_id',
        'Project ID',
        'feedback_fish_project_id_field',
        'feedback_fish',
        'feedback_fish_general'
    );
    add_settings_field(
        'feedback_fish_selected_menu',
        'Assigned Menu',
        'feedback_fish_selected_menu_field',
        'feedback_fish',
        'feedback_fish_general'
    );
    register_setting('feedback_fish', 'feedback_fish_project_id');
    register_setting('feedback_fish', 'feedback_fish_selected_menu');
}

function feedback_fish_selected_menu_field()
{
    $menus = wp_get_nav_menus();
    $selected_menu = get_option('feedback_fish_selected_menu');

    echo "<select name=\"feedback_fish_selected_menu\" id=\"feedback_fish_selected_menu\">";
    echo "<option value=\"\">No menu (manual)</option>";
    foreach ($menus as $menu) {
        echo "<option value=\"$menu->slug\"" .
            selected($selected_menu, $menu->slug, false) .
            ">$menu->name</option>";
    }

    echo "</select>";
    echo "<p class=\"description\">The plugin will add a \"Send feedback\" button to the menu you select here. If you do not select a menu, you have to add the <code>data-feedback-fish</code> HTML attribute to a button of your choice yourself. (learn more about manual usage in <a href=\"https://feedback.fish/help/widget/\" target=\"_blank\">the documentation</a>)</p>";
}

function feedback_fish_project_id_field()
{
    echo '<input name="feedback_fish_project_id" id="feedback_fish_project_id" type="text" value="' .
        get_option('feedback_fish_project_id') .
        '" />';
    echo '<p class="description">You can get your project ID from <a href=\"https://feedback.fish/app\" target=\"_blank\">your Feedback Fish dashboard</a>.</p>';
}

function feedback_fish_settings_page_section_callback()
{
}
?>
