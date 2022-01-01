---
title: 'PHP编码标准修正器PHP-CS-Fixer'
categories:
  - php
tags:
  - php
  - laravel
toc: true
author: 虢國技酱
comments: true
date: 2021-03-19 15:34:49
description:
original:
permalink:
---

## 前言

在日常开发中，无论是个人项目还是团队项目，我们都希望编码的风格保持一致，代码更加优雅；为了这个目标，项目通常会制定一系列编码规范以及注意事项，比如 `=`两边的空格；虽然我们每次开发都小心翼翼的注意这些，但还是难免出现“失误”。

我们希望能有一个工具，它可以按照我们的项目规范自动格式化我们的代码，在我们保存文件时或者git-commit时触发，对我们的代码进行格式化，节省我们的编码负担和心理负担。

<!-- more -->

## PHP-CS-Fixer
> PHP-CS-Fixer即PHP编码标准修正器，用来解决PHP项目开发过程中团队标准和规范问题。

这个工具就是PHP编码标准修正器 [FriendsOfPHP/PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer)
> - 官网 [cs.symfony.com](https://cs.symfony.com/)
> - GitHub [FriendsOfPHP/PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer)
> - GitHub star超过10K，应该说是比较认可的PHP编码规范工具

- PHP-CS-Fixer [用法参考](https://github.com/FriendsOfPHP/PHP-CS-Fixer/blob/2.18/doc/usage.rst)
- PHP-CS-Fixer 内置的规则设置参考 [内置规则列表](https://github.com/FriendsOfPHP/PHP-CS-Fixer/blob/2.18/doc/rules/index.rst)
- PHP-CS-Fixer 支持自定义规则 [参考创建自定义规则](https://github.com/FriendsOfPHP/PHP-CS-Fixer/blob/2.18/doc/custom_rules.rst)，利用这个功能我们就可以实现符合我们项目规范的修正器规则集了。
- PHP-CS-Fixer 通过在项目的根目录下的`.php_cs.dist`和`.php_cs`; 其中`.php_cs.dist`作为项目配置，`.php_cs`作为本地配置 [配置文件参考](https://github.com/FriendsOfPHP/PHP-CS-Fixer/blob/2.18/doc/config.rst)
- PHP-CS-Fixer 支持composer安装
- PHP-CS-Fixer 支持插件的IDE包括：
	* [Atom](https://github.com/Glavin001/atom-beautify)
	* [NetBeans](http://plugins.netbeans.org/plugin/49042/php-cs-fixer)
	* [PhpStorm](https://medium.com/@valeryan/how-to-configure-phpstorm-to-use-php-cs-fixer-1844991e521f)
	* [Sublime Text](https://github.com/benmatselby/sublime-phpcs)
	* [Vim](https://github.com/stephpy/vim-php-cs-fixer)
	* [VS Code](https://github.com/junstyle/vscode-php-cs-fixer)


## Laravel编码风格规则集
从头编写或者整理一套我们的代码规范规则集实在有点“繁琐”，优秀的做饭应该是“站在巨人的肩上”！比如PHP开发现在用的最多的框架应该是Laravel了，我们可以基于laravel的编码规则集，来定制我们自己的规则集。

laravel框架规则集：[laravel-shift/.php_cs.laravel.php](https://gist.github.com/laravel-shift/cab527923ed2a109dda047b97d53c200)
```php
<?php

use PhpCsFixer\Config;
use PhpCsFixer\Finder;

$rules = [
    'array_syntax' => ['syntax' => 'short'],
    'binary_operator_spaces' => [
        'default' => 'single_space',
        'operators' => ['=>' => null]
    ],
    'blank_line_after_namespace' => true,
    'blank_line_after_opening_tag' => true,
    'blank_line_before_statement' => [
        'statements' => ['return']
    ],
    'braces' => true,
    'cast_spaces' => true,
    'class_attributes_separation' => [
        'elements' => ['method']
    ],
    'class_definition' => true,
    'concat_space' => [
        'spacing' => 'none'
    ],
    'declare_equal_normalize' => true,
    'elseif' => true,
    'encoding' => true,
    'full_opening_tag' => true,
    'fully_qualified_strict_types' => true, // added by Shift
    'function_declaration' => true,
    'function_typehint_space' => true,
    'heredoc_to_nowdoc' => true,
    'include' => true,
    'increment_style' => ['style' => 'post'],
    'indentation_type' => true,
    'linebreak_after_opening_tag' => true,
    'line_ending' => true,
    'lowercase_cast' => true,
    'lowercase_constants' => true,
    'lowercase_keywords' => true,
    'lowercase_static_reference' => true, // added from Symfony
    'magic_method_casing' => true, // added from Symfony
    'magic_constant_casing' => true,
    'method_argument_space' => true,
    'native_function_casing' => true,
    'no_alias_functions' => true,
    'no_extra_blank_lines' => [
        'tokens' => [
            'extra',
            'throw',
            'use',
            'use_trait',
        ]
    ],
    'no_blank_lines_after_class_opening' => true,
    'no_blank_lines_after_phpdoc' => true,
    'no_closing_tag' => true,
    'no_empty_phpdoc' => true,
    'no_empty_statement' => true,
    'no_leading_import_slash' => true,
    'no_leading_namespace_whitespace' => true,
    'no_mixed_echo_print' => [
        'use' => 'echo'
    ],
    'no_multiline_whitespace_around_double_arrow' => true,
    'multiline_whitespace_before_semicolons' => [
        'strategy' => 'no_multi_line'
    ],
    'no_short_bool_cast' => true,
    'no_singleline_whitespace_before_semicolons' => true,
    'no_spaces_after_function_name' => true,
    'no_spaces_around_offset' => true,
    'no_spaces_inside_parenthesis' => true,
    'no_trailing_comma_in_list_call' => true,
    'no_trailing_comma_in_singleline_array' => true,
    'no_trailing_whitespace' => true,
    'no_trailing_whitespace_in_comment' => true,
    'no_unneeded_control_parentheses' => true,
    'no_unreachable_default_argument_value' => true,
    'no_useless_return' => true,
    'no_whitespace_before_comma_in_array' => true,
    'no_whitespace_in_blank_line' => true,
    'normalize_index_brace' => true,
    'not_operator_with_successor_space' => true,
    'object_operator_without_whitespace' => true,
    'ordered_imports' => ['sortAlgorithm' => 'alpha'],
    'phpdoc_indent' => true,
    'phpdoc_inline_tag' => true,
    'phpdoc_no_access' => true,
    'phpdoc_no_package' => true,
    'phpdoc_no_useless_inheritdoc' => true,
    'phpdoc_scalar' => true,
    'phpdoc_single_line_var_spacing' => true,
    'phpdoc_summary' => true,
    'phpdoc_to_comment' => true,
    'phpdoc_trim' => true,
    'phpdoc_types' => true,
    'phpdoc_var_without_name' => true,
    'psr4' => true,
    'self_accessor' => true,
    'short_scalar_cast' => true,
    'simplified_null_return' => false, // disabled by Shift
    'single_blank_line_at_eof' => true,
    'single_blank_line_before_namespace' => true,
    'single_class_element_per_statement' => true,
    'single_import_per_statement' => true,
    'single_line_after_imports' => true,
    'single_line_comment_style' => [
        'comment_types' => ['hash']
    ],
    'single_quote' => true,
    'space_after_semicolon' => true,
    'standardize_not_equals' => true,
    'switch_case_semicolon_to_colon' => true,
    'switch_case_space' => true,
    'ternary_operator_spaces' => true,
    'trailing_comma_in_multiline_array' => true,
    'trim_array_spaces' => true,
    'unary_operator_spaces' => true,
    'visibility_required' => [
        'elements' => ['method', 'property']
    ],
    'whitespace_after_comma_in_array' => true,
];

$project_path = getcwd();
$finder = Finder::create()
    ->in([
        $project_path . '/app',
        $project_path . '/config',
        $project_path . '/database',
        $project_path . '/resources',
        $project_path . '/routes',
        $project_path . '/tests',
    ])
    ->name('*.php')
    ->notName('*.blade.php')
    ->ignoreDotFiles(true)
    ->ignoreVCS(true);

return Config::create()
    ->setFinder($finder)
    ->setRules($rules)
    ->setRiskyAllowed(true)
    ->setUsingCache(true);
```

- 解读
	- `$rules` 规则集
	- `$project_path` 项目目录
	- `$finder` 规则应用范围
	- `return Config::create()` php-cs-fixer工具要求的返回
- 问题：我在应用中发现，有可能php-cs-fixer工具会报错，注意检查`$project_path`和`$finder`；
	- 有些老版本的laravel目录结构不是`$finder`里面的形式可能导致php-cs-fixer报错
	- 有些可能是因为`$project_path`报错
	  ![enter image description here](/images/php/php-cs-fixer/getcwd-DIR.png)

## 团队编码规则集

团队编码规则集应该是一个**基础**+**实践**的结果。

在项目伊始，团队规则集一片空白，以前也没有相关可以借鉴的规则时；应该有一个最简略版本的规则集设定，然后在编码过程中，团队成员不断地发现规则问题，进行全员讨论，调整或者开发自定义规则，然后全员推广。
