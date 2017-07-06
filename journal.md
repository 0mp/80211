- `iwl_hwrate_to_plcp_idx`

  - [ ] Let's see what is stored inside `rate_n_flags` and what calls `iwl_hwrate_to_plcp_idx`:

    - `rs_rate_from_ucode_rate`

      This function calls `iwl_hwrate_to_plcp_idx` passing `ucode_rate` as a argument.

      - [ ] What is ucode_rate?

        - `iwl_mvm_rs_tx_status`

          This function calls `rs_rate_from_ucode_rate` passing `tx_resp_hwrate` as the `ucode_rate` argument.

          - [ ] What is `tx_resp_hwrate`?

            It is set by this line:

            ```c
            u32 tx_resp_hwrate = (uintptr_t)info->status.status_driver_data[1];
            ```

            - [ ] What is `status_driver_data[1]`?

              - In `mvm/tx.c:iwl_mvm_rx_tx_cmd_single`:

                ```c
                info->status.status_driver_data[1] =
                        (void *)(uintptr_t)le32_to_cpu(tx_resp->initial_rate);
                ```

                - What is `tx_resp->initial_rate`?

                  `struct iwl_mvm_tx_resp` notifies that firmware is transmitting a packet.

                  The `initial_rate` member is described as

                  > For non-agg: rate of the successful Tx.
                  > For agg: rate of the Tx of all the batch. RATE_MCS_*

                  In `iwl_mvm_rx_tx_cmd_single` the `initial_rate` comes from the `data` member of `struct iwl_rx_packet`.

                  - `iwl_mvm_rx_tx_cmd`

                    It is a function which calls either `iwl_mvm_rx_tx_cmd_single` or `iwl_mvm_rx_tx_cmd_agg` depending on the `tx_resp->frame_count` value.

                It looks like `tx_resp` is the data of a packet. (?)

              - In `mvm/tx.c:iwl_mvm_tx_reclaim`:

                ```c
                ba_info->status.status_driver_data[1] = (void *)(uintptr_t)rate;
                ```

    - `rs_pretty_print_rate`

      It is a debug function. Skipping...
