
+++
date = '2020-08-14'
draft = true
title = 'Future-proofing software'
+++
(0.1)

_Overengineering_ a bad or wrong design makes _future-proofing_ that much more
difficult. Like reinforcing a home to withstand a hurricane, using stronger
materials will only allow the structure to withstand winds up to a certain
magnitude. To withstand stronger winds the entire design needs to change,
necessitating razing the home to its foundations and starting again.

Future-proofing is not about adding a features that you assume your users may
ask for in the future. These features will go unused, effectively cluttering
your business logic (requiring testing prior to each release, or being hidden by
sweeping under a carpet of feature flags), or most likely when the feature is
requested the requirements will be so different as to necessitate a rewrite.

Future-proofing is removing all unnecessary features.

