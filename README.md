<mj-section background-color="#eeeeee" padding="14px">
      <mj-column padding="0">
        <mj-table width="100%" cellpadding="0" cellspacing="0" role="presentation">
          <tr>
            <!-- LEFT IMAGE -->
            <td width="130" valign="top" style="padding-right:14px;">
              <table role="presentation" cellpadding="0" cellspacing="0" width="130">
                <tr>
                  <td
                    width="130"
                    height="280"
                    align="center"
                    valign="middle"
                    style="
                      width:130px;
                      height:280px;
                      background:#ff4d4d;
                      border-radius:16px;
                      color:#ffffff;
                      font-family:Arial, sans-serif;
                      font-size:13px;
                      text-align:center;
                    "
                  >
                    Image Placeholder
                  </td>
                </tr>
              </table>
            </td>

            <!-- RIGHT CONTENT -->
            <td valign="top">
              <table width="100%" cellpadding="0" cellspacing="0" role="presentation">

                <!-- HEADLINE -->
                <tr>
                  <td
                    style="
                      font-family:Arial, sans-serif;
                      font-size:15px;
                      font-weight:bold;
                      line-height:20px;
                      color:#2b2b2b;
                      padding-bottom:10px;
                    "
                  >
                   {{ hook.headline }}
                  </td>
                </tr>

                <!-- QUESTIONS -->
                <tr>
              <td>
                <table width="100%" cellpadding="0" cellspacing="0" role="presentation">
                  {% for question in hook.questions %}
                  <tr>
                    <!-- ICON -->
                    <td width="28" valign="top" style="padding:0 8px 14px 0;">
                      <div
                        style="
                          font-family:Georgia, 'Times New Roman', serif;
                          font-size:26px;
                          font-weight:bold;
                          color:#c7c7c7;
                          line-height:26px;
                        "
                      >
                        ?
                      </div>
                    </td>
                    <!-- QUESTION TEXT -->
                    <td
                      valign="top"
                      style="
                        font-family:Arial, sans-serif;
                        font-size:13px;
                        font-weight:bold;
                        line-height:18px;
                        color:#3a3a3a;
                        padding-bottom:14px;
                      "
                    >
                      {{ question }}
                    </td>
                  </tr>
                  {% endfor %}
                </table>
              </td>
            </tr>

                <!-- CTA BUTTON -->
               <tr>
              <td style="padding-top:2px;">
                <a href="{{ hook.cta_url }}" style="text-decoration:none;">
                  <table role="presentation" cellpadding="0" cellspacing="0">
                    <tr>
                      <td
                        style="
                          background:#f2b400;
                          border-radius:24px;
                          padding:10px 8px 10px 20px;
                        "
                      >
                        <table role="presentation" cellpadding="0" cellspacing="0">
                          <tr>
                            <td
                              style="
                                font-family:Arial, sans-serif;
                                font-size:14px;
                                font-weight:bold;
                                color:#1a1a1a;
                                padding-right:12px;
                                white-space:nowrap;
                              "
                            >
                              {{ hook.cta_text | default('Читать') }}
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
        </mj-table>
      </mj-column>
    </mj-section>
