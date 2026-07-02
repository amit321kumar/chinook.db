<mjml>
  <mj-head>
    <mj-style>
      /* ============================================================
         RESPONSIVE OVERRIDES — the hook body below is built with raw
         HTML tables (not mj-section/mj-column), so MJML's automatic
         mobile stacking doesn't apply here. These media queries do it
         manually. Every selector needs !important because it's
         overriding inline styles / HTML width|height attributes, which
         otherwise win by specificity in most rendering engines.
         ============================================================ */
      @media only screen and (max-width:480px) {
        .hook-outer-table { width:100% !important; }

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
          font-size:19px !important;
          line-height:1.3 !important;
        }

        /* Option pairs: each option (icon+text) is its own <td> that
           stacks full-width on mobile. The icon+text nested table
           inside each option-cell is left alone, so the bullet and
           its text stay side by side even while the pair stacks. */
        .hook-option-cell {
          display:block !important;
          width:100% !important;
          padding:0 0 14px 0 !important;
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
              <v:rect xmlns:v="urn:schemas-microsoft-com:vml" fill="true" stroke="false" style="width:220px; height:283px;">
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
                  <td style="padding:26px 24px;">
                    <table width="100%" cellpadding="0" cellspacing="0" role="presentation">

                      <!-- QUESTION -->
                      <tr>
                        <td
                          class="hook-question"
                          style="
                            font-family:Arial, sans-serif;
                            font-size:22px;
                            font-weight:bold;
                            line-height:1.3;
                            color:#f5b800;
                            padding-bottom:20px;
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
                        <td class="hook-option-cell" width="50%" valign="top" style="padding:0 16px 18px 0;">
                          <table role="presentation" cellpadding="0" cellspacing="0" width="100%">
                            <tr>
                              <td width="24" valign="top" style="padding-right:8px;">
                                <div style="width:16px; height:16px; border-radius:50%; background:#f5c000;">&nbsp;</div>
                              </td>
                              <td valign="top" style="
                                  font-family:Arial, sans-serif;
                                  font-size:16px;
                                  font-weight:bold;
                                  line-height:1.35;
                                  color:#ffffff;
                                ">
                                Faster symptom control
                              </td>
                            </tr>
                          </table>
                        </td>

                        <td class="hook-option-cell" width="50%" valign="top" style="padding:0 0 18px 0;">
                          <table role="presentation" cellpadding="0" cellspacing="0" width="100%">
                            <tr>
                              <td width="24" valign="top" style="padding-right:8px;">
                                <div style="width:16px; height:16px; border-radius:50%; background:#f5c000;">&nbsp;</div>
                              </td>
                              <td valign="top" style="
                                  font-family:Arial, sans-serif;
                                  font-size:16px;
                                  font-weight:bold;
                                  line-height:1.35;
                                  color:#ffffff;
                                ">
                                Lower sedation burden
                              </td>
                            </tr>
                          </table>
                        </td>
                      </tr>
                      <tr>
                        <td class="hook-option-cell" width="50%" valign="top" style="padding:0 16px 18px 0;">
                          <table role="presentation" cellpadding="0" cellspacing="0" width="100%">
                            <tr>
                              <td width="24" valign="top" style="padding-right:8px;">
                                <div style="width:16px; height:16px; border-radius:50%; background:#f5c000;">&nbsp;</div>
                              </td>
                              <td valign="top" style="
                                  font-family:Arial, sans-serif;
                                  font-size:16px;
                                  font-weight:bold;
                                  line-height:1.35;
                                  color:#ffffff;
                                ">
                                Flexible dosing options
                              </td>
                            </tr>
                          </table>
                        </td>

                        <td class="hook-option-cell" width="50%" valign="top" style="padding:0 0 18px 0;">
                          <table role="presentation" cellpadding="0" cellspacing="0" width="100%">
                            <tr>
                              <td width="24" valign="top" style="padding-right:8px;">
                                <div style="width:16px; height:16px; border-radius:50%; background:#f5c000;">&nbsp;</div>
                              </td>
                              <td valign="top" style="
                                  font-family:Arial, sans-serif;
                                  font-size:16px;
                                  font-weight:bold;
                                  line-height:1.35;
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
                        <td style="padding-top:6px;">
                          <a href="https://example.com" style="text-decoration:none;">
                            <table class="hook-cta-table" role="presentation" cellpadding="0" cellspacing="0">
                              <tr>
                                <td
                                  style="
                                    background:#f2b400;
                                    border-radius:24px;
                                    padding:10px 8px 10px 20px;
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
                                        width="26"
                                        height="26"
                                        align="center"
                                        valign="middle"
                                        style="
                                          background:#ffffff;
                                          border-radius:50%;
                                          width:26px;
                                          height:26px;
                                          font-family:Arial, sans-serif;
                                          font-size:14px;
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
