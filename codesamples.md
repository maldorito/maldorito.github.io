---
layout: page
title: Code Samples
permalink: /codesamples/
published: true
---

## [Code Samples](https://github.com/maldorito/codesamples)

Here are some code samples from previous workplace projects that I no longer have repo access to but still wanted to showcase.

### ToyInsider ACF blocks

#### Ad Block

PHP
```
<?php
/**
 * BLOCK - Renders an Ad block.
 *
 * @package apb
 */

use function WebDevStudios\apb\get_acf_fields;
use function WebDevStudios\apb\setup_block_defaults;

$apb_block = isset( $block ) ? $block : '';
$apb_args  = isset( $args ) ? $args : '';

$apb_defaults = [
	'class'               => [ 'wds-block', 'wds-block-adblock' ],
	'id'                  => ( isset( $block ) && ! empty( $block['anchor'] ) ) ? $block['anchor'] : '',
	'allowed_innerblocks' => [ 'adsanity/single-ad' ],
];

// Apply the Gutenberg classes to the $apb_defaults array.
if ( ! empty( $apb_block['backgroundColor'] ) ) {
	$apb_defaults['class'][] = 'has-background';
	$apb_defaults['class'][] = 'has-' . $apb_block['backgroundColor'] . '-background-color';
}

// Returns updated $apb_defaults array with classes from Gutenberg or from the print_block() function.
// Returns formatted attributes as $apb_atts array.
[ $apb_defaults, $apb_atts ] = setup_block_defaults( $apb_args, $apb_defaults, $apb_block );

$apb_block_fields = get_acf_fields( [ 'block_or_shortcode', 'ad_shortcode', 'background_image' ], $block['id'] );
$apb_bg_image_id  = $apb_block_fields['background_image'];
$apb_bg_image_url = wp_get_attachment_image_url( $apb_bg_image_id, 'full' );
$apb_bg           = $apb_bg_image_url ? 'background-image: url(' . $apb_bg_image_url . ')' : '';
$apb_ad_block     = $apb_block_fields['block_or_shortcode'];
$apb_shortcode    = $apb_block_fields['ad_shortcode'];
$apb_shortcode    = str_replace( 'alignnone', 'aligncenter', $apb_shortcode );

?>

<div <?php echo $apb_atts; // phpcs:ignore WordPress.Security.EscapeOutput.OutputNotEscaped ?> style="<?php echo esc_attr( $apb_bg ); ?>">
	<div class="adblock-header" aria-label="Ad Block Header"><?php esc_html_e( 'Advertisement', 'apb' ); ?></div>
		<div class="adblock-ad" aria-label="Ad Block Advertisement">
			<?php
			if ( ! $apb_block_fields['block_or_shortcode'] ) {
				if ( ! empty( $apb_defaults['allowed_innerblocks'] ) ) :
					echo '<InnerBlocks allowedBlocks="' . esc_attr( wp_json_encode( $apb_defaults['allowed_innerblocks'] ) ) . '" />';
				endif;
			} else {
				if ( ! empty( 'ad_shortcode' ) ) {
					echo do_shortcode( $apb_shortcode );
				}
			}
			?>
		</div>
</div>
```

SCSS
```
//----------------------------------------
// Block - Ad block Styles, FRONTEND
//----------------------------------------

@screen max-tablet-portrait {
	.adblock-header {
		@apply text-xs;
	}
	.adblock-ad {
		div[class*='ad-'] {
			@apply h-fit;
		}

		.acf-innerblocks-container {
			div[class*='ad-'] {
				@apply h-fit;
			}
		}
	}
}

.wds-block-adblock {
	@apply container flex flex-col relative bg-cover bg-center;

	.adblock-header {
		@apply absolute left-1/2 -top-32 -translate-x-1/2 uppercase text-center text-gray py-10;
	}
}
```

#### Inquiries Block

#### Newsletter Block

#### Related Mini Block

#### Three Latest Posts Block

___
