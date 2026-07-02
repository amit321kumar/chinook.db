<mjml>
  <mj-head>
    <mj-style>
      /* Force predictable column widths: with the default table-layout:auto,
         browsers can size a text-heavy column much narrower than expected
         when nested tables (icon+text pairs) sit inside it — this is the
         real cause of the question/options wrapping too tightly, not font
         size. Fixed layout allocates the two explicit-width columns (image
         220px, spacer 16px) exactly that, and gives 100% of the remainder
         to the content column, every time. */
      .hook-outer-table {
        table-layout:fixed;
      }

      /* ============================================================
         RESPONSIVE OVERRIDES — the hook body below is built with raw
         HTML tables (not mj-section/mj-column), so MJML's automatic
         mobile stacking doesn't apply here. These media queries do it
         manually. Every selector needs !important because it's
         overriding inline styles / HTML width|height attributes, which
         otherwise win by specificity in most rendering engines.
         ============================================================ */
      @media only screen and (max-width:480px) {
        .hook-outer-table { width:100% !important; table-layout:fixed !important; }

        /* Stack image above content */
        .hook-image-cell,
        .hook-content-cell {
          display:block !important;
          width:100% !important;
        }
        .hook-image-cell {
          padding:0 !important;
          height:200px !important;
          background-size:cover !important;
          background-position:center !important;
        }
        .hook-spacer-cell {
          display:none !important;
          width:0 !important;
          line-height:0 !important;
          font-size:0 !important;
        }
        .hook-content-cell {
          padding-top:16px !important;
        }

        /* Question */
        .hook-question {
          font-size:17px !important;
          line-height:1.3 !important;
        }

        /* Option pairs: each option (icon+text) is its own <td> that
           stacks full-width on mobile. The icon+text nested table
           inside each option-cell is left alone, so the bullet and
           its text stay side by side even while the pair stacks. */
        .hook-option-cell {
          display:block !important;
          width:100% !important;
          padding:0 0 10px 0 !important;
        }

        /* CTA goes full width */
        .hook-cta-table {
          width:100% !important;
        }
        .hook-cta-table td {
          width:auto !important;
        }
      }
    </mj-style>
  </mj-head>

  <mj-body background-color="#f4f4f4">
    <mj-section background-color="#eeeeee" padding="14px">
      <mj-column padding="0">
        


        <mj-table
          css-class="hook-outer-table"
          width="100%"
          cellpadding="0"
          cellspacing="0"
          role="presentation"
        >
          <tr>
            <!-- LEFT IMAGE
                 Height now matches the content column exactly in every
                 client except Outlook desktop: the background-image/color
                 is set directly on this <td> (not a nested div/table), and
                 a table cell's background always paints across its full row
                 height — no height:100% percentage trick required, since
                 that's just default table cell rendering. Outlook desktop
                 (mso conditional below) can't do this because it renders
                 backgrounds via VML shapes, which need an explicit height,
                 so it falls back to the estimated image_h.
                 A dedicated spacer column replaces the old padding-right,
                 since padding on a cell that also carries a background
                 would shrink the visible rounded box inward. -->
            <td
              class="hook-image-cell"
              width="220"
              valign="top"
              align="center"
              style="
                background:#dcdcdc;
                border-radius:16px;
              "
            >
              <!--[if mso]>
              <v:rect xmlns:v="urn:schemas-microsoft-com:vml" fill="true" stroke="false" style="width:220px; height:227px;">
                <v:fill color="#dcdcdc" />
                <v:textbox inset="0,0,0,0"><div>&nbsp;</div></v:textbox>
              </v:rect>
              <![endif]-->
              <!--[if !mso]><!-->
              <span style="display:inline-block; padding:14px 8px; color:#7a7a7a; font-family:Arial, sans-serif; font-size:13px;">Image Placeholder</span>
              <!--<![endif]-->
            </td>

            <!-- SPACER: fixed-width gutter between image and content, kept
                 separate so the image td's background isn't pushed inward
                 by padding -->
            <td class="hook-spacer-cell" width="16" style="font-size:1px; line-height:1px;">&nbsp;</td>

            <!-- RIGHT: TEAL QUESTION BOX -->
            <td class="hook-content-cell" valign="top">
              <table
                width="100%"
                cellpadding="0"
                cellspacing="0"
                role="presentation"
                style="background:#1a7f8c; border-radius:16px;"
              >
                <tr>
                  <td style="padding:20px 20px;">
                    <table width="100%" cellpadding="0" cellspacing="0" role="presentation">

                      <!-- QUESTION -->
                      <tr>
                        <td
                          class="hook-question"
                          style="
                            font-family:Arial, sans-serif;
                            font-size:19px;
                            font-weight:bold;
                            line-height:1.3;
                            color:#f5b800;
                            padding-bottom:14px;
                          "
                        >
                          What is your top treatment priority for allergic rhinitis?
                        </td>
                      </tr>

                      <!-- OPTIONS: rendered two per row on desktop, each
                           pair stacks to full width on mobile via
                           .hook-option-cell. Icon+text for a single option
                           live in their own nested table so they never
                           split apart when the pair stacks. -->
                      <tr>
                        <td class="hook-option-cell" width="50%" valign="top" style="padding:0 16px 12px 0;">
                          <table role="presentation" cellpadding="0" cellspacing="0" width="100%">
                            <tr>
                              <td width="24" valign="top" style="padding-right:8px;">
                                <div style="width:14px; height:14px; border-radius:50%; background:#f5c000;">&nbsp;</div>
                              </td>
                              <td valign="top" style="
                                  font-family:Arial, sans-serif;
                                  font-size:15px;
                                  font-weight:bold;
                                  line-height:1.3;
                                  color:#ffffff;
                                ">
                                Faster symptom control
                              </td>
                            </tr>
                          </table>
                        </td>

                        <td class="hook-option-cell" width="50%" valign="top" style="padding:0 0 12px 0;">
                          <table role="presentation" cellpadding="0" cellspacing="0" width="100%">
                            <tr>
                              <td width="24" valign="top" style="padding-right:8px;">
                                <div style="width:14px; height:14px; border-radius:50%; background:#f5c000;">&nbsp;</div>
                              </td>
                              <td valign="top" style="
                                  font-family:Arial, sans-serif;
                                  font-size:15px;
                                  font-weight:bold;
                                  line-height:1.3;
                                  color:#ffffff;
                                ">
                                Lower sedation burden
                              </td>
                            </tr>
                          </table>
                        </td>
                      </tr>
                      <tr>
                        <td class="hook-option-cell" width="50%" valign="top" style="padding:0 16px 12px 0;">
                          <table role="presentation" cellpadding="0" cellspacing="0" width="100%">
                            <tr>
                              <td width="24" valign="top" style="padding-right:8px;">
                                <div style="width:14px; height:14px; border-radius:50%; background:#f5c000;">&nbsp;</div>
                              </td>
                              <td valign="top" style="
                                  font-family:Arial, sans-serif;
                                  font-size:15px;
                                  font-weight:bold;
                                  line-height:1.3;
                                  color:#ffffff;
                                ">
                                Flexible dosing options
                              </td>
                            </tr>
                          </table>
                        </td>

                        <td class="hook-option-cell" width="50%" valign="top" style="padding:0 0 12px 0;">
                          <table role="presentation" cellpadding="0" cellspacing="0" width="100%">
                            <tr>
                              <td width="24" valign="top" style="padding-right:8px;">
                                <div style="width:14px; height:14px; border-radius:50%; background:#f5c000;">&nbsp;</div>
                              </td>
                              <td valign="top" style="
                                  font-family:Arial, sans-serif;
                                  font-size:15px;
                                  font-weight:bold;
                                  line-height:1.3;
                                  color:#ffffff;
                                ">
                                Sustained day-long relief
                              </td>
                            </tr>
                          </table>
                        </td>
                      </tr>

                      <!-- CTA BUTTON -->
                      <tr>
                        <td style="padding-top:2px;">
                          <a href="https://example.com" style="text-decoration:none;">
                            <table class="hook-cta-table" role="presentation" cellpadding="0" cellspacing="0">
                              <tr>
                                <td
                                  style="
                                    background:#f2b400;
                                    border-radius:22px;
                                    padding:8px 6px 8px 16px;
                                  "
                                >
                                  <table role="presentation" cellpadding="0" cellspacing="0" width="100%">
                                    <tr>
                                      <td
                                        align="center"
                                        style="
                                          font-family:Arial, sans-serif;
                                          font-size:14px;
                                          font-weight:bold;
                                          color:#1a1a1a;
                                          padding-right:12px;
                                          white-space:nowrap;
                                        "
                                      >
                                        Learn more
                                      </td>
                                      <td
                                        width="22"
                                        height="22"
                                        align="center"
                                        valign="middle"
                                        style="
                                          background:#ffffff;
                                          border-radius:50%;
                                          width:22px;
                                          height:22px;
                                          font-family:Arial, sans-serif;
                                          font-size:13px;
                                          font-weight:bold;
                                          color:#f2b400;
                                        "
                                      >
                                        &#8250;
                                      </td>
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


      </mj-column>
    </mj-section>
  </mj-body>
</mjml>
