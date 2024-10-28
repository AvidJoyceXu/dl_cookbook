# Lists of interesiting pytorch features and problems
## `nn.ModuleDict`
```Python
import torch.nn as nn
class GPT(nn.Module):
    def __init__(self, config):
        super().__init__()
        self.transformer = nn.ModuleDict(dict(
            wte = nn.Embedding(config.vocab_size, config.n_embd),
            wpe = nn.Embedding(config.block_size, config.n_embed),
            h = nn.moduleList([Blok(config) for _ in range(config.n_layer)]), # List
            ln_f = nn.LayerNorm(config.n_embed),
        ))
        self.lm_head = nn.Linear(config.n_embd, config.vocab_size, bias=False)
        # Project the output embedding to the vocab. Size = 50257.
```

