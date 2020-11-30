====
A mixer based on stacked generalization, using neural networks. Used in GeCo3 and AC2 (variant).

Export mixer to other compressors
----
The mixer interface and implementation is done in:
```
mix.h
mix.c
```

The neural network implementation used by the mixer is done in:
```
nn.h
nn.c
```

`fastonebigheader.h` contains fast math functions, and is used by the neural network and the mixer.

With these five files integrated into the project, the compressor now needs to `#include "mix.h"` in the file where the mixing will occur.

The mixer is initialized with: `mix_state_t *mxs = mix_init(NUMBER_OF_MODELS, ALPHABET_SIZE, NUMBER_OF_HIDDEN_NODES);`

Every time a new symbol is seen the mixing is done with `const float* y = mix(mxs, probs);`. 

`probs[MODEL_IDX][SYMBOL_IDX]`is a matrix with the probabilities (]0,1[) for each model and for each symbol in the alphabet. The return value contains the non-normalized probabilities for each symbol. One way of normalizing these probabilities is to divide each by the total sum.

Training must be done after invoking the mixing with `mix_update_state(mxs, probs, sym, lr);`. Where `sym` is the actual symbol seen encoded as an int [0, ALPHABET_SIZE[ and`lr`is the learning rate.

Finally, the mixer can be freed with `mix_free(mxs);`
