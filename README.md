{# ==============================================================
   HOOK — body-only fragment, meant to be injected into an
   mj-section/mj-column in the master email template. It does NOT
   include <mjml>/<mj-head>/<mj-body> tags itself since it's one of
   several hook variants picked at random for a given send.

   REQUIRED in the master template's <mj-head><mj-style>:
     {% include "partials/hook_head_styles.mjml" %}
   That partial holds table-layout:fixed and the @media block this
   fragment's classes (hook-outer-table, hook-image-cell,
   hook-content-cell, hook-spacer-cell, hook-question,
   hook-option-cell, hook-cta-table) rely on for mobile stacking.
   It only needs to be included once regardless of which hook
   variant ends up in the body — unused classes are harmless.
   ============================================================== #}
{% set headline_cpl = 27 %}
{% set option_cpl = 15 %}
{% set headline_lines = ((hook.question|length + headline_cpl - 1) // headline_cpl) %}
{% if headline_lines < 1 %}{% set headline_lines = 1 %}{% endif %}
{% set ns = namespace(total_option_lines=0) %}
{% for i in range(0, hook.options|length, 2) %}
{% set a_lines = ((hook.options[i].text|length + option_cpl - 1) // option_cpl) %}
{% if a_lines < 1 %}{% set a_lines = 1 %}{% endif %}
{% if hook.options[i + 1] is defined %}
{% set b_lines = ((hook.options[i + 1].text|length + option_cpl - 1) // option_cpl) %}
{% if b_lines < 1 %}{% set b_lines = 1 %}{% endif %}
{% else %}
{% set b_lines = 0 %}
{% endif %}
{% set row_lines = [a_lines, b_lines] | max %}
{% set ns.total_option_lines = ns.total_option_lines + row_lines %}
{% endfor %}
{% set num_rows = ((hook.options|length + 1) // 2) %}
{% set computed_image_height_raw = 40 + (headline_lines * 25) + 14 + (ns.total_option_lines * 19) + (num_rows * 12) + 38 %}
{% set computed_image_height = (computed_image_height_raw * 0.85) | round | int %}
{% if computed_image_height < 220 %}{% set computed_image_height = 220 %}{% endif %}
{% if computed_image_height > 460 %}{% set computed_image_height = 460 %}{% endif %}
{% set image_h = hook.image_height | default(computed_image_height) %}
<mj-table css-class="hook-outer-table" width="100%" cellpadding="0" cellspacing="0" role="presentation">
<tr>
<td class="hook-image-cell" width="220" valign="top" align="center" style="{% if hook.image_url %}background-image:url('{{ hook.image_url }}'); background-size:cover; background-position:center;{% else %}background:#dcdcdc;{% endif %} border-radius:16px;">
<!--[if mso]>
<v:rect xmlns:v="urn:schemas-microsoft-com:vml" fill="true" stroke="false" style="width:220px; height:{{ image_h }}px;">
{% if hook.image_url %}<v:fill type="frame" src="{{ hook.image_url }}" color="#eeeeee" />{% else %}<v:fill color="#dcdcdc" />{% endif %}
<v:textbox inset="0,0,0,0"><div>&nbsp;</div></v:textbox>
</v:rect>
<![endif]-->
<!--[if !mso]><!-->
{% if not hook.image_url %}<span style="display:inline-block; padding:14px 8px; color:#7a7a7a; font-family:Arial, sans-serif; font-size:13px;">Image Placeholder</span>{% else %}&nbsp;{% endif %}
<!--<![endif]-->
</td>
<td class="hook-spacer-cell" width="16" style="font-size:1px; line-height:1px;">&nbsp;</td>
<td class="hook-content-cell" valign="top">
<table width="100%" cellpadding="0" cellspacing="0" role="presentation" style="background:#1a7f8c; border-radius:16px;">
<tr>
<td style="padding:20px 20px;">
<table width="100%" cellpadding="0" cellspacing="0" role="presentation">
<tr>
<td colspan="2" class="hook-question" style="font-family:Arial, sans-serif; font-size:19px; font-weight:bold; line-height:1.3; color:#f5b800; text-align:center; padding-bottom:14px;">{{ hook.question }}</td>
</tr>
<tr>
{# LEFT COLUMN: options at index 0, 2, 4... stacked vertically in one cell.
   NOTE on mobile ordering: because this is now a real 2-column grid (one
   <td> per column, not one <td> per option), stacking on mobile reads
   column 1 fully then column 2 (e.g. options 1,3 then 2,4) rather than
   natural reading order 1,2,3,4. If reading order matters more than a
   strict 2-col grid, the row-per-pair layout (one <tr> per option pair)
   preserves 1,2,3,4 order when stacked — ask if you'd rather have that. #}
<td class="hook-option-cell" width="50%" valign="top" style="padding:0 16px 0 0;">
<table role="presentation" cellpadding="0" cellspacing="0" width="100%">
{% for i in range(0, hook.options|length, 2) %}
<tr>
<td width="24" valign="top" style="padding-right:8px; padding-bottom:12px;"><div style="width:14px; height:14px; border-radius:50%; background:#f5c000;">&nbsp;</div></td>
<td valign="top" style="font-family:Arial, sans-serif; font-size:15px; font-weight:bold; line-height:1.3; color:#ffffff; padding-bottom:12px;">{{ hook.options[i].text }}</td>
</tr>
{% endfor %}
</table>
</td>
{# RIGHT COLUMN: options at index 1, 3, 5... stacked vertically in one cell #}
<td class="hook-option-cell" width="50%" valign="top" style="padding:0 0 0 0;">
<table role="presentation" cellpadding="0" cellspacing="0" width="100%">
{% for i in range(1, hook.options|length, 2) %}
<tr>
<td width="24" valign="top" style="padding-right:8px; padding-bottom:12px;"><div style="width:14px; height:14px; border-radius:50%; background:#f5c000;">&nbsp;</div></td>
<td valign="top" style="font-family:Arial, sans-serif; font-size:15px; font-weight:bold; line-height:1.3; color:#ffffff; padding-bottom:12px;">{{ hook.options[i].text }}</td>
</tr>
{% endfor %}
</table>
</td>
</tr>
<tr>
<td colspan="2" style="padding-top:2px;">
<a href="{{ hook.cta_url }}" style="text-decoration:none;">
<table class="hook-cta-table" role="presentation" cellpadding="0" cellspacing="0">
<tr>
<td style="background:#f2b400; border-radius:22px; padding:8px 6px 8px 16px;">
<table role="presentation" cellpadding="0" cellspacing="0" width="100%">
<tr>
<td align="center" style="font-family:Arial, sans-serif; font-size:14px; font-weight:bold; color:#1a1a1a; padding-right:12px; white-space:nowrap;">{{ hook.cta_text | default('Learn more') }}</td>
<td width="22" height="22" align="center" valign="middle" style="background:#ffffff; border-radius:50%; width:22px; height:22px; font-family:Arial, sans-serif; font-size:13px; font-weight:bold; color:#f2b400;">&#8250;</td>
</tr>
</table>
</td>
</tr>
</table>
</a>
</td>
</tr>
</table>
</td>
</tr>
</table>
</td>
</tr>
</mj-table>
{% if show_mlr_bbox_email_hook %}
<mj-raw><div style="position:relative; height:0;"><span style="position:absolute; bottom:0px; right:0px; background:{{ mlr_bbox_email_hook_color }};">HOOK</span></div></mj-raw>
{% endif %}
