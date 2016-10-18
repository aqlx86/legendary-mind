# Legendary Mind

Legendary Mind is an easy to use Neural Network written in PHP
  - Wrapper Class For Handling The Network
  - Archive And Restore Neural Networks Serialized In Text-Files

### Installation
```bash
composer require devtronic/legendary-mind
```

### Usage
#### Standalone Network
```php
<?php

use Devtronic\LegendaryMind\Mind;
use Devtronic\LegendaryMind\Topology;

require_once 'vendor/autoload.php';

// Activation derivative function
function tanh_prime($y)
{
    return 1.0 - pow($y, 2);
}

// Create the topology for the net
$topology = new Topology(2, 3, 1, 1);

// Set the activation function
$activation = 'tanh';
$activation_derivative = 'tanh_prime';

// Instantiate the Mind
$mind = new Mind($topology, $activation, $activation_derivative);

// Setup XOR Lessons
$lessons = [
    [
        [0, 0], # Inputs
        [0]     # Outputs
    ],
    [
        [0, 1], # Inputs
        [1]     # Outputs
    ],
    [
        [1, 0], # Inputs
        [1]     # Outputs
    ],
    [
        [1, 1], # Inputs
        [0]     # Outputs
    ],
];

// Train the lessons
$mind->train($lessons);

// Setup the check lesson
$test = [1, 1];
$expected = [0];

// Propagate the check lesson
$mind->propagate($test);

// Print the Output
print_r($mind->getOutput());

// Backpropagate
$mind->backPropagate($expected);

```

#### With Wrapper (recommended)
```php
<?php

use Devtronic\LegendaryMind\Wrapper;

require_once 'vendor/autoload.php';

// Activation derivative function
function tanh_prime($y)
{
    return 1.0 - pow($y, 2);
}

// Set the activation function
$activation = 'tanh';
$activation_derivative = 'tanh_prime';

$wrapper = new Wrapper($hiddenNeurons = 3, $hiddenLayers = 1);

// Possible input values
$properties = [
    'color' => ['red', 'pink', 'blue', 'green'],
    'hair_length' => ['short', 'long']
];

$outputs = [
    'gender' => ['male', 'female']
];

// Setup the wrapper
$wrapper->initialize($properties, $outputs, $activation, $activation_derivative);

// Setup the lessons
$lessons = [
    [
        'input' => [
            'color' => 'red',
            'hair_length' => 'long',
        ],
        'output' => [
            'gender' => 'female'
        ],
    ],
    [
        'input' => [
            'color' => 'blue',
            'hair_length' => 'short',
        ],
        'output' => [
            'gender' => 'male'
        ],
    ],
    [
        'input' => [
            'color' => 'red',
            'hair_length' => 'short',
        ],
        'output' => [
            'gender' => 'male'
        ],
    ],
];

// Train the lessons
$wrapper->train($lessons);

// Setup the check lesson
$test_lesson = [
    'input' => [
        'color' => ['pink', 'green'],
        'hair_length' => 'long',
    ],
    'output' => [
        'gender' => 'female'
    ]
];

// Propagate the check lesson
$wrapper->propagate($test_lesson);

// Print the Output
print_r($wrapper->getResult());

// Backpropagate
$wrapper->backPropagate($test_lesson);
```

#### Archive and restore the network (only with wrapper, Line 23 and Line 74)
```php
<?php

use Devtronic\LegendaryMind\Wrapper;

require_once 'vendor/autoload.php';

// Activation derivative function
function tanh_prime($y)
{
    return 1.0 - pow($y, 2);
}

// Set the activation function
$activation = 'tanh';
$activation_derivative = 'tanh_prime';

$wrapper = new Wrapper($hiddenNeurons = 3, $hiddenLayers = 1);

$network_file = 'network.txt';

if (is_file($network_file)) {
    // Restore the Network
    $wrapper->restore($network_file);
} else {

    // Possible input values
    $properties = [
        'color' => ['red', 'pink', 'blue', 'green'],
        'hair_length' => ['short', 'long']
    ];

    $outputs = [
        'gender' => ['male', 'female']
    ];

    // Setup the wrapper
    $wrapper->initialize($properties, $outputs, $activation, $activation_derivative);

    // Setup the lessons
    $lessons = [
        [
            'input' => [
                'color' => 'red',
                'hair_length' => 'long',
            ],
            'output' => [
                'gender' => 'female'
            ],
        ],
        [
            'input' => [
                'color' => 'blue',
                'hair_length' => 'short',
            ],
            'output' => [
                'gender' => 'male'
            ],
        ],
        [
            'input' => [
                'color' => 'red',
                'hair_length' => 'short',
            ],
            'output' => [
                'gender' => 'male'
            ],
        ],
    ];

    // Train the lessons
    $wrapper->train($lessons);

    // Archive the Network
    $wrapper->archive($network_file);
}

// Setup the check lesson
$test_lesson = [
    'input' => [
        'color' => ['pink', 'green'],
        'hair_length' => 'long',
    ],
    'output' => [
        'gender' => 'female'
    ]
];

// Propagate the check lesson
$wrapper->propagate($test_lesson);

// Print the Output
print_r($wrapper->getResult());

// Backpropagate
$wrapper->backPropagate($test_lesson);
```