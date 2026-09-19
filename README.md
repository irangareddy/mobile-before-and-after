# mobile-before-and-after

An agent skill for capturing reproducible before-and-after evidence from native
iOS and Android apps.

It treats screenshots as runtime proof: the skill aligns app state, device
configuration, data fixtures, and navigation before comparing visual or
semantic changes. It also distinguishes Simulator/emulator verification from
physical-device, distribution, and production proof.

## Install

```bash
npx skills add https://github.com/irangareddy/mobile-before-and-after \
  --skill mobile-before-and-after
```

## Use

```text
Use $mobile-before-and-after to prove this event-detail fix on iOS and Android,
using Web as the expected semantic reference.
```

The skill can capture either platform independently or produce one labeled
before-and-after pair per platform. When the
[`before-and-after`](https://www.skills.sh/vercel-labs/before-and-after/before-and-after)
skill is installed, it delegates GitHub PR formatting and attachment handling
to that skill.
