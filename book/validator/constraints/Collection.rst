Collection
==========

.. Validates array entries against different constraints.

異なる制約に対する配列のエントリのバリデーションを実行します。

.. code-block:: yaml

    - Collection:
        fields:
            key1:
                - NotNull: ~
            key2:
                - MinLength: 10

オプション
----------

.. * ``fields`` (required): An associative array of array keys and one or more
     constraints
   * ``allowMissingFields``: Whether some of the keys may not be present in the
     array. Default: ``false``
   * ``allowExtraFields``: Whether the array may contain keys not present in the
     ``fields`` option. Default: ``false``
   * ``missingFieldsMessage``: The error message if the ``allowMissingFields``
     validation fails
   * ``allowExtraFields``: The error message if the ``allowExtraFields`` validation
     fails

* ``fields`` (必須): 配列のキーと1つ以上の制約を結合した配列。
* ``allowMissingFields``: 配列内のいくつかのキーが与えられていないかどうか。デフォルト: ``false``
* ``allowExtraFields``: 配列が ``fields`` オプションにないキーを含んでいないかどうか。デフォルト: ``false``
* ``missingFieldsMessage``: ``allowMissingFields`` バリデーションが失敗した時のエラーメッセージ。
* ``allowExtraFields``: ``allowExtraFields`` バリデーションが失敗した時のエラーメッセージ。

例:
---

Let's validate an array with two indexes ``firstName`` and ``lastName``. The
value of ``firstName`` must not be blank, while the value of ``lastName`` must
not be blank with a minimum length of four characters. Furthermore, both keys
may not exist in the array.

.. configuration-block::

    .. code-block:: yaml

        # Sensio/HelloBundle/Resources/config/validation.yml
        Sensio\HelloBundle\Author:
            properties:
                options:
                    - Collection:
                        fields:
                            firstName:
                                - NotBlank: ~
                            lastName:
                                - NotBlank: ~
                                - MinLength: 4
                        allowMissingFields: true

    .. code-block:: xml

        <!-- Sensio/HelloBundle/Resources/config/validation.xml -->
        <class name="Sensio\HelloBundle\Author">
            <property name="options">
                <constraint name="Collection">
                    <option name="fields">
                        <value key="firstName">
                            <constraint name="NotNull" />
                        </value>
                        <value key="lastName">
                            <constraint name="NotNull" />
                            <constraint name="MinLength">4</constraint>
                        </value>
                    </option>
                    <option name="allowMissingFields">true</option>
                </constraint>
            </property>
        </class>

    .. code-block:: php-annotations

        // Sensio/HelloBundle/Author.php
        class Author
        {
            /**
             * @validation:Collection(
             *   fields = {
             *     "firstName" = @validation:NotNull(),
             *     "lastName" = { @validation:NotBlank(), @validation:MinLength(4) }
             *   },
             *   allowMissingFields = true
             * )
             */
            private $options = array();
        }

    .. code-block:: php

        // Sensio/HelloBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\Collection;
        use Symfony\Component\Validator\Constraints\NotNull;
        use Symfony\Component\Validator\Constraints\NotBlank;
        use Symfony\Component\Validator\Constraints\MinLength;

        class Author
        {
            private $options = array();

            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addPropertyConstraint('options', new Collection(array(
                    'fields' => array(
                        'firstName' => new NotNull(),
                        'lastName' => array(new NotBlank(), new MinLength(4)),
                    ),
                    'allowMissingFields' => true,
                )));
            }
        }

.. The following object would fail the validation.

以下のオブジェクトでは、バリデーションは失敗します。

.. code-block:: php

    $author = new Author();
    $author->options['firstName'] = null;
    $author->options['lastName'] = 'foo';

    print $validator->validate($author);

.. You should see the following error messages:

以下のエラーメッセージが表示されるでしょう。

.. code-block:: text

    Sensio\HelloBundle\Author.options[firstName]:
        This value should not be null
    Sensio\HelloBundle\Author.options[lastName]:
        This value is too short. It should have 4 characters or more
