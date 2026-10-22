# @red-cup-engineering/tidepool-keepers

<!-- 561-group-standards:badges:start -->
[![Red Cup Engineering](https://img.shields.io/badge/561%20Group-Red%20Cup%20Engineering-c0392b)](https://github.com/red-cup-engineering)
[![License: MIT](https://img.shields.io/badge/license-MIT-c0392b)](LICENSE)
[![Indigenous data: CARE](https://img.shields.io/badge/Indigenous%20data-CARE-4b6f44)](https://github.com/red-cup-engineering/.github/blob/main/GOVERNANCE.md)
[![Tips welcome](https://img.shields.io/badge/tips-welcome-d4a017)](https://github.com/561-group/.github/blob/main/TIPS.md)
<!-- 561-group-standards:badges:end -->

The permissionless bots: cycle close, overdue marking, question quiescence, head relay, and later worklist and H¹ submission. Anyone may run them; the house runs them on systemd timers in the shape `cold-red-gate` already uses. Signing goes through `key-management-services-section`'s `sign-with-wallet` under a bounded mandate (allowed contracts and selectors, zero native value); every keeper emits a paper intent and a receipt when no mandate is armed. A keeper never holds pool funds and never has a privileged role; every call it makes is one any member could make.
