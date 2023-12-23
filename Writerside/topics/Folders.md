# Folders and Files

- Folders name must be in **PascalCase**;
- CS scripts must be in **PascalCase**;
```
Assets/
└── Scripts
    └── PlayerController.cs
```
- Animations must specify the **GameObject** name as a prefix and **Anim** or **Animation** as a suffix;
- Animations controller must be named the **same** as the GameObject;
```
Assets/
└── Animations
    └── Player.controller
    └── PlayerJumpAnimation.anim
```

## Non-Code Assets
- Use **tree_small** not **small_tree**, this will group all "tree" objects together instead of all "small" objects.
```
Assets/
└── Sprites
    └── tree_small.png
    └── tree_large.png
```
