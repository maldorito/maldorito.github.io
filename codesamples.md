---
layout: page
title: Code Samples
permalink: /codesamples/
published: true
---

> Below are some code samples from previous workplace projects I want to showcase.

## [GitHub Repo](https://github.com/maldorito/codesamples)

### ToyInsider ACF blocks



#### Rotating Ad Block

![Rotating Ad Block #1](/images/Rotating%20Ad%20Block%20#1.gif)

![Rotating Ad Block #2](/images/Rotating%20Ad%20Block%20#2.gif)

PHP
```php
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

SASS
```sass
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

![Inquiries block](/images/inquiriesblock.webp)

PHP
```php
<?php
/**
 * Your block render code goes here.
 *
 * @package apb
 */

use function WebDevStudios\apb\get_acf_fields;
use function WebDevStudios\apb\setup_block_defaults;

$apb_block = isset( $block ) ? $block : '';
$apb_args  = isset( $args ) ? $args : '';

$apb_defaults = [
	'class' => [ 'wds-block', 'wds-block-inquiries' ],
];

// Returns updated $apb_defaults array with classes from Gutenberg or from the print_block() function.
// Returns formatted attributes as $apb_atts array.
[ $apb_defaults, $apb_atts ] = setup_block_defaults( $apb_args, $apb_defaults, $apb_block );

$apb_block_fields = get_acf_fields( [ 'intro_text', 'contact_person' ], $block['id'] );
$apb_intro        = $apb_block_fields['intro_text'];
$apb_contacts     = $apb_block_fields['contact_person'];

?>

<div <?php echo $apb_atts; // phpcs:ignore WordPress.Security.EscapeOutput.OutputNotEscaped ?>>
	<div class="inquiries-content">
		<div class="inquiries-intro" aria-label="Contact Notice"><?php echo esc_html( $apb_block_fields['intro_text'] ); ?></div>
		<div class="inquiries-people">
			<?php foreach ( $apb_contacts as $apb_contact ) : ?>
				<div class="inquiries-person">
					<?php $apb_info = $apb_contact['contact_info']; ?>
					<div class="name-and-title" aria-label="Name and Title"><?php echo esc_html( $apb_contact['name'] . ' | ' . $apb_contact['title'] ); ?></div>
						<div class="contact-info">
							<ul class="contact-column-one">
								<?php foreach ( $apb_info as $apb_info_entries ) : ?>
								<li class="icon-and-name">
									<span class="contact-icon"><?php echo wp_get_attachment_image( $apb_info_entries['icon'], 'icon' ); ?></span>
									<span class="contact-method" aria-label="Contact Method"><?php echo esc_html( $apb_info_entries['contact_method'] ); ?></span>
								</li>
								<?php endforeach; ?>
							</ul>
							<ul class="contact-column-two">
								<?php foreach ( $apb_info as $apb_info_entries ) : ?>

								<li>
									<span class="contact-detail" aria-label="Contact Detail">
										<?php
										if ( 'Email' === $apb_info_entries['contact_method'] ) {
											echo esc_html( $apb_info_entries['contact_email'] );
										} else {
											echo esc_html( $apb_info_entries['contact_number'] );
										}
										?>
									</span>
								</li>
								<?php endforeach; ?>
							</ul>
						</div><!-- .contact-info -->
				</div><!-- .inquiries-person -->
			<?php endforeach; ?>
		</div><!-- .inquiries-people -->
	</div><!-- .inquiries-content -->
</div>
```
SASS
```sass
//----------------------------------------
// Block - Inquiries Styles, FRONTEND
//----------------------------------------

@screen max-tablet-portrait {

	.wds-block {
		@apply my-24;
	}
	.wds-block-inquiries {
		@apply w-full mx-20;

		.inquiries-content{
			@apply flex flex-col w-full;

			.inquiries-intro {
				@apply flex flex-row capitalize;
				font-family: var( --wp--preset--font-family--headline-heavy );
				font-size: var( --wp--preset--font-size--headline-20 );
				font-weight: 900;
			}
			.inquiries-people {
				@apply flex flex-row justify-between flex-wrap;

				.inquiries-person {
					@apply flex flex-col mt-40;

					.name-and-title {
						@apply flex flex-row pb-20 flex-nowrap;
						font-family: var( --wp--preset--font-family--headline-reg );
					}
					.contact-info {
						@apply flex;

						.contact-column-one {
							@apply flex flex-col mb-0 mr-20;

							.icon-and-name {
								@apply flex flex-row;

								.contact-icon {
									@apply flex flex-col justify-center pr-2;
								}
								.contact-method {
									@apply flex flex-col;
									font-family: var( --wp--preset--font-family--headline-heavy );
								}
							}
						}
						.contact-column-two {
							@apply flex flex-col mb-0;

							.contact-detail {
								@apply flex;
								font-family: var( --wp--preset--font-family--headline-reg );
							}
						}
					}
				}
			}
		}
	}
}

@screen tablet-portrait {

	.wds-block {
		@apply my-24;
	}
	.wds-block-inquiries {
		@apply w-full mx-20;

		.inquiries-content{
			@apply flex flex-col w-full;

			.inquiries-intro {
				@apply flex flex-row capitalize;
				font-family: var( --wp--preset--font-family--headline-heavy );
				font-size: var( --wp--preset--font-size--headline-20 );
				font-weight: 900;
			}
			.inquiries-people {
				@apply flex flex-row justify-between flex-wrap;

				.inquiries-person {
					@apply flex flex-col basis-1/3 mt-20;

					.name-and-title {
						@apply flex flex-row pb-20 flex-nowrap;
						font-family: var( --wp--preset--font-family--headline-reg );
					}
					.contact-info {
						@apply flex;

						.contact-column-one {
							@apply flex flex-col mb-0 mr-20;

							.icon-and-name {
								@apply flex flex-row;

								.contact-icon {
									@apply flex flex-col justify-center pr-2;
								}
								.contact-method {
									@apply flex flex-col;
									font-family: var( --wp--preset--font-family--headline-heavy );
								}
							}
						}
						.contact-column-two {
							@apply flex flex-col mb-0;

							.contact-detail {
								@apply flex;
								font-family: var( --wp--preset--font-family--headline-reg );
							}
						}
					}
				}
			}
		}
	}
}
```

#### Newsletter Block

![Newsletter block](/images/newsletterblock.webp)

PHP
```php
<?php
/**
 * Your block render code goes here.
 *
 * @package apb
 */

use function WebDevStudios\apb\get_acf_fields;
use function WebDevStudios\apb\setup_block_defaults;

$apb_block = isset( $block ) ? $block : '';
$apb_args  = isset( $args ) ? $args : '';

$apb_defaults = [
	'class'               => [ 'wds-block', 'wds-block-newsletter' ],
	'allowed_innerblocks' => [ 'constant-contact/single-contact-form', 'core/legacy-widget', 'gravityforms/form' ],
];

// Apply the Gutenberg classes to the $apb_defaults array.
if ( ! empty( $apb_block['backgroundColor'] ) ) {
	$apb_defaults['class'][] = 'has-background';
	$apb_defaults['class'][] = 'has-' . $apb_block['backgroundColor'] . '-background-color';
}

// Returns updated $apb_defaults array with classes from Gutenberg or from the print_block() function.
// Returns formatted attributes as $apb_atts array.
[ $apb_defaults, $apb_atts ] = setup_block_defaults( $apb_args, $apb_defaults, $apb_block );

$apb_block_fields = get_acf_fields( [ 'background_image', 'newsletter_headline' ], $block['id'] );
$apb_bg_image_id  = $apb_block_fields['background_image'];
$apb_bg_image_url = wp_get_attachment_image_url( $apb_bg_image_id, 'full' );
$apb_bg           = $apb_bg_image_url ? 'background-image: url(' . $apb_bg_image_url . ')' : '';

?>

<div <?php echo $apb_atts; // phpcs:ignore WordPress.Security.EscapeOutput.OutputNotEscaped ?> style="<?php echo esc_attr( $apb_bg ); ?>;">
	<div class="newsletter-content">
		<div class="newsletter-headline">
			<h1 aria-label="Newsletter Headline"><?php echo esc_html( $apb_block_fields['newsletter_headline'] ); ?></h1>
		</div>
		<div class="newsletter-form">
			<?php
			if ( ! empty( $apb_defaults['allowed_innerblocks'] ) ) :
				echo '<InnerBlocks allowedBlocks="' . esc_attr( wp_json_encode( $apb_defaults['allowed_innerblocks'] ) ) . '" />';
				endif;
			?>
		</div>
	</div>
</div>
```

SASS
```sass
//----------------------------------------
// Block - Newsletter Styles, FRONTEND
//----------------------------------------
@import 'widget';

@screen max-tablet-portrait {
	.wds-block-newsletter {
		@apply container px-24 py-24;
		max-width: 354px;

		.newsletter-content {
			@apply flex flex-col;

			.newsletter-headline {
				@apply flex uppercase pb-4;
				color: var( --wp--preset--color--white );
				line-height: 1.23;

				h1 {
					font-size: var( --wp--preset--font-size--medium-25 );
				}
			}

			.newsletter-form {
				@apply w-full;

				.acf-innerblocks-container {
					div {
						.gform_confirmation_message {
							color: var( --wp--preset--color--white );
						}
					}
					form {
						.gform-body {
							@apply pb-16;
							.gform_fields {
								.gfield {
									.ginput_container {
										input.large {
											@apply w-full py-12 border text-center;
											border-color: var( --wp--preset--color--primary );
											background-color: var( --wp--preset--color--white );
											font-family: var( --wp--preset--font-family--headline-reg );
										}
									}
								}
							}
						}
						.gform_footer {
							@apply m-0 p-0 ;
							input {
								@apply w-full mb-0 py-12 font-black uppercase;
								background-color: var( --wp--preset--color--primary );
								color: var( --wp--preset--color--black );
								font-family: var( --wp--preset--font-family--headline-reg );
							}
						}
					}
				}

				span.ctct-label-top {
					display: none;
				}

				.ctct-form-wrapper {
					@apply w-full mb-0;

					p.ctct-form-field {
						@apply w-full;
					}

					input.ctct-email {
						@apply w-full border text-center;
						border-color: var( --wp--preset--color--primary );
						background-color: var( --wp--preset--color--white );
						font-family: var( --wp--preset--font-family--headline-reg );
					}

					p.ctct-form-field-submit {
						@apply w-full mb-0;
					}

					input.ctct-submit {
						@apply w-full font-black;
						background-color: var( --wp--preset--color--primary );
						color: var( --wp--preset--color--black );
						font-family: var( --wp--preset--font-family--headline-reg );
					}
					.ctct-disclosure {
						hr {
							display: none;
						}
						small {
							color: var( --wp--preset--color--white );
						}
					}
				}
			}
		}
	}
}

@screen tablet-portrait {
	.wds-block-newsletter {
		@apply container px-24 py-40;
		max-width: 354px;

		.newsletter-content {
			@apply flex flex-col;

			.newsletter-headline {
				@apply flex uppercase pb-48;
				color: var( --wp--preset--color--white );
				line-height: 1.23;

				h1 {
					font-size: var( --wp--preset--font-size--xlarge-39 );
				}
			}

			.newsletter-form {
				@apply w-full;

				.acf-innerblocks-container {
					div {
						.gform_confirmation_message {
							color: var( --wp--preset--color--white );
						}
					}
					form {
						.gform-body {
							@apply pb-16;
							.gform_fields {
								.gfield {
									.ginput_container {
										input.large {
											@apply w-full py-12 border text-center;
											border-color: var( --wp--preset--color--primary );
											background-color: var( --wp--preset--color--white );
											font-family: var( --wp--preset--font-family--headline-reg );
										}
									}
								}
							}
						}
						.gform_footer {
							@apply m-0 p-0 ;
							input {
								@apply w-full mb-0 py-12 font-black uppercase;
								background-color: var( --wp--preset--color--primary );
								color: var( --wp--preset--color--black );
								font-family: var( --wp--preset--font-family--headline-reg );
							}
						}
					}
				}

				span.ctct-label-top {
					display: none;
				}

				.ctct-form-wrapper {
					@apply w-full mb-0;

					p.ctct-form-field {
						@apply w-full;
					}

					input.ctct-email {
						@apply w-full border text-center;
						border-color: var( --wp--preset--color--primary );
						background-color: var( --wp--preset--color--white );
						font-family: var( --wp--preset--font-family--headline-reg );
					}

					p.ctct-form-field-submit {
						@apply w-full mb-0;
					}

					input.ctct-submit {
						@apply w-full font-black;
						background-color: var( --wp--preset--color--primary );
						color: var( --wp--preset--color--black );
						font-family: var( --wp--preset--font-family--headline-reg );
					}
					.ctct-disclosure {
						hr {
							display: none;
						}
						small {
							color: var( --wp--preset--color--white );
						}
					}
				}
			}
		}
	}
}
```

#### Related Mini Block

![Related Mini block](/images/relatedminiblock.webp)

PHP
```php
<?php
/**
 * BLOCK - Renders a Related Post
 *
 * @package apb
 */

use function WebDevStudios\apb\get_acf_fields;
use function WebDevStudios\apb\setup_block_defaults;

$apb_block = isset( $block ) ? $block : '';
$apb_args  = isset( $args ) ? $args : '';

$apb_defaults = [
	'class' => [ 'wds-block', 'wds-block-related-mini' ],
];

$apb_block_fields    = get_acf_fields( [ 'related_mini_header', 'related_mini_header_tag', 'related_mini_title' ], $block['id'] );
$apb_related_post_id = $apb_block_fields['related_mini_title'];
$apb_header_tag      = '';
?>

<div id="related-mini_<?php echo esc_attr( $block['id'] ); ?>" class="related-mini-content">
	<div class="related-col-1">
		<div class="related-mini-image" alt="Related Post Image">
			<a href="<?php echo esc_html( get_permalink( $apb_related_post_id[0] ) ); ?>"><?php echo get_the_post_thumbnail( $apb_related_post_id[0] ); ?></a>
		</div>
	</div>
	<div class="related-col-2">
		<div class="related-mini-header" aria-label="Related Post Header">
			<?php
			switch ( $apb_block_fields['related_mini_header_tag'] ) {
				case 'h2':
					$apb_header_tag = 'h2';
					break;
				case 'h3':
					$apb_header_tag = 'h3';
					break;
				case 'h4':
					$apb_header_tag = 'h4';
					break;
				default:
					$apb_header_tag = 'h2';
					break;
			}
			?>
			<<?php echo esc_attr( $apb_header_tag ); ?>><?php echo esc_html( $apb_block_fields['related_mini_header'] ); ?></<?php echo esc_attr( $apb_header_tag ); ?>>
		</div>
		<div class="related-mini-title" aria-label="Related Post Title">
			<a href="<?php echo esc_html( get_permalink( $apb_related_post_id[0] ) ); ?>"><?php echo esc_html( get_the_title( $apb_related_post_id[0] ) ); ?></a>
		</div>
	</div>
</div>
```

SASS
```sass
//----------------------------------------
// Block - Related Mini Styles, FRONTEND
//----------------------------------------

.related-mini-content {
	@apply border-2 border-secondary py-20 flex pl-20 relative;

	.link {
		@apply absolute left-0 top-0 w-full h-full;
	}

	.related-col-2 {
		@apply px-40 w-full flex flex-col gap-5 justify-center;
	}

	.related-mini-image {
		@apply w-[206px];
	}

	.related-mini-header {
		h2,
		h3,
		h4 {
			@apply leading-[48px] uppercase m-0;

			font-family: var( --wp--preset--font-family--headline-heavy );
			font-size: var( --wp--preset--font-size--xlarge-39 );
		}
	}

	.related-mini-title {
		a {
			@apply leading-6 no-underline text-secondary;
			font-family: var( --wp--preset--font-family--headline-heavy );
			font-size: var( --wp--preset--font-size--medium );
		}
	}

	// Mobile
	@screen max-tablet-portrait {
		@apply px-20;

		.related-col-1 {
			@apply hidden;
		}

		.related-col-2 {
			@apply px-0;
		}

		.related-mini-header {
			h2,
			h3,
			h4 {
				@apply leading-[38px];

				font-size: var( --wp--preset--font-size--large-31 );
			}
		}
	}

	@screen desktop-max {
		@apply mx-16;
	}
}
```

#### Three Latest Posts Block

![Three Latest Posts block](/images/latestpostsblock.webp)

PHP
```php
<?php
/**
 * Your block render code goes here.
 *
 * @package apb
 */

use function WebDevStudios\apb\get_acf_fields;
use function WebDevStudios\apb\print_module;
use function WebDevStudios\apb\setup_block_defaults;

$apb_block = isset( $block ) ? $block : '';
$apb_args  = isset( $args ) ? $args : '';

$apb_defaults = [
	'class' => [ 'wds-block', 'wds-block-three-latest-posts' ],
];

// Returns updated $apb_defaults array with classes from Gutenberg or from the print_block() function.
// Returns formatted attributes as $apb_atts array.
[ $apb_defaults, $apb_atts ] = setup_block_defaults( $apb_args, $apb_defaults, $apb_block );

// Pull in the fields from ACF, if we've not pulled them in using print_block().
$apb_block_fields  = get_acf_fields( [ 'icon', 'headline', 'headline_type', 'cta', 'hide_on_mobile' ], $block['id'] );
$apb_icon_headline = [
	'icon'          => $apb_block_fields['icon'],
	'headline_type' => $apb_block_fields['headline_type'],
	'headline'      => $apb_block_fields['headline'],
];

switch ( $apb_icon_headline['headline_type'] ) {
	case '2':
		$apb_post_title = 'h3';
		break;
	case '3':
		$apb_post_title = 'h4';
		break;
	case '4':
		$apb_post_title = 'h5';
		break;
	case '5':
		$apb_post_title = 'h6';
		break;
	case '6':
		$apb_post_title = 'p';
		break;
	default:
		$apb_post_title = 'h3';
		break;
}

$apb_cta               = $apb_block_fields['cta'];
$apb_post_reading_time = do_shortcode( '[rt_reading_time"]' );
$apb_time_svg_path     = get_template_directory_uri() . '/build/images/icons/time_purple.svg';

// Checks for hide on mobile toggle and adds the style to the block container attributes.
if ( $apb_block_fields['hide_on_mobile'] ) {
	$apb_atts = str_replace( 'posts', 'posts mobile-hidden', $apb_atts );
}

?>

<div <?php echo $apb_atts; // phpcs:ignore WordPress.Security.EscapeOutput.OutputNotEscaped ?>>
	<?php
	$apb_recent_posts = wp_get_recent_posts(
		array(
			'numberposts' => 3,
			'post_status' => 'publish',
		)
	);
	?>
	<?php
	if ( $apb_icon_headline ) :
		print_module(
			'icon-headline',
			$apb_icon_headline
		);
	endif;
	?>
	<?php foreach ( $apb_recent_posts as $apb_post_item ) : ?>
		<div class="latest-post">
			<a href="<?php echo esc_html( get_permalink( $apb_post_item['ID'] ) ); ?>">
				<div class="latest-post-image-content">
					<div class="reading-time">
						<img src="<?php echo esc_url( $apb_time_svg_path ); ?>" alt="Time" />
						<?php echo esc_html( $apb_post_reading_time . 'm' ); ?>
					</div>
					<div class="latest-post-image">
						<?php echo get_the_post_thumbnail( $apb_post_item['ID'], 'full' ); ?>
					</div>
				</div>
				<<?php echo esc_html( $apb_post_title ); ?> class="latest-post-title" aria-label="Post Title"><?php echo esc_html( $apb_post_item['post_title'] ); ?></<?php echo esc_html( $apb_post_title ); ?>>
			</a>
		</div>
	<?php endforeach; ?>
	<span class="latest-posts-cta"><a href="<?php echo esc_url( get_post_type_archive_link( 'post' ) ); ?>"><?php echo esc_html( $apb_cta ); ?>&rarr;</a></span>
</div>
```

SASS
```sass
//----------------------------------------
// Block - Three Latest Posts Styles, FRONTEND
//----------------------------------------

@screen max-tablet-portrait {
	.mobile-hidden {
		display: none;
	}
}

.wds-block-three-latest-posts {
	@apply max-w-sm;

	.latest-post {
		@apply mb-24;

		a {
			@apply no-underline;

			.latest-post-image-content {
				@apply relative flex mx-0 pb-16 justify-end w-full;

				.reading-time {
					@apply flex flex-row justify-items-center space-x-4 mt-12 mr-12 px-6 py-2 absolute z-10 bg-white;
					color: var( --wp--preset--color--secondary );
				}

				.latest-post-image {
					@apply z-0 w-full;

					img {
						@apply flex relative w-full object-cover;
					}
				}
			}
			.latest-post-title {
				@apply capitalize;
				font-size: var( --wp--preset--font-size--headline-20 );
				font-family: var( --wp--preset--font-family--headline-heavy );
			}
		}

		a:hover {
			@apply underline;
		}
	}
	.latest-posts-cta {
		@apply flex justify-end;
		font-size: var( --wp--preset--font-size--normal );

		a {
			@apply uppercase no-underline;
			color: var( --wp--preset--color--secondary );
			font-family: var( --wp--preset--font-family--headline-heavy );
		}

		a:hover {
			@apply underline;
		}
	}
}
```
___
