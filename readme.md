# Log Exp Args

## Requirements

- loguru
- tabulate

## Example of Exp code

- `main.py`

```python
import os
import argparse
from loguru import logger
from tabulate import tabulate

def setup_logger(output=None, distributed_rank=0):
    logger.remove()
    if output is not None:
        if output.endswith(".txt") or output.endswith(".log"):
            filename = output
        else:
            filename = os.path.join(output, "log_running.txt")
        ensure_dir(os.path.dirname(filename))
        logger.add(filename)


def get_args_parser():
    parser = argparse.ArgumentParser('Example Exp', add_help=False)
    parser.add_argument('output_dir', default="logs", type=str)
    parser.add_argument('--lr', default=0.01, type=float)
    parser.add_argument('--batch_size', default=16, type=int)
    parser.add_argument('--arch', default='ArchX', type=str, help="Name of the architecture")
    return parser

@logger.catch
def main(args):
    # setup logger
    setup_logger(args.output_dir)
    # log args
    logger.info("\n{}".format(tabulate([(k, v) for k,v in vars(args).items()])))
    # training code
    # ...

if __name__ == '__main__':
    parser = argparse.ArgumentParser('Example', parents=[get_args_parser()])
    args = parser.parse_args()
    if not os.path.exists(args.output_dir):
        os.mkdir(args.output_dir)
    main(args)
```

```bash
python main.py --output_dir logs --lr 0.01 --batch_size 16 --arch ArchX
```

## saved log file

```
2022-05-24 14:27:29.304 | INFO     | __main__:main:29 - 
----------  -----
output_dir  logs
lr          0.01
batch_size  16
arch        ArchX
----------  -----
```
