<?php

/**
 * Вспомогательные методы для работы с sql-запрсами и результатами выполнения запросов
 * 
 * @author sshp
 */

class pgHelpers
{

    /**
     * Возвращает строку экранированных (и залючённых в кавычки) значений через запятую.
     * Подходит для "IN (...)".
     *
     * @param array $values_array - одномерный массив
     * @return string
     * @throws InvalidArgumentException - если передан многомерный массив
     */
    public function quoteValuesList($values_array)
    {
        $quoted_values = array();
        foreach ($values_array as $val)
        {
            $quoted_values[] = $this->quoteValue($val);
        }
        return implode(', ', $quoted_values);
    }

    /**
     * Возвращает массив экранированных и залючённых в кавычки значений из переданного массива
     * (например, для дальнейшего implode)
     * Ключи массива не обрабатываются!
     *
     * @param array $values_array - одномерный массив.
     * @param bool $keep_keys - если true, вернёт массив с теми же ключами (не обработанными!), иначе - численно индексированный.
     * @return array
     */
    public function quoteInArray($values_array, $keep_keys = false)
    {
        $escaped_values = array();
        $i = 0;
        foreach ($values_array as $key => $value)
        {
            $key = ($keep_keys ? $key : $i++);
            $escaped_values[$key] = $this->quoteValue($value);
        }
        return $escaped_values;
    }

    /**
     * Подготавливает значение для включения в sql-запрос -
     * экранирует и заключает в кавычки, если нужно.
     * Справляется даже с false, null, NaN и INF (infinite) (возвращает строки с соответствующими константами для PostgreSQL)
     *
     * @param string|int|float|bool|null $value
     * @return string|int|float
     * @throws InvalidArgumentException - если передан array, object или resource
     */
    public function quoteValue($value)
    {
        if (is_string($value))
        {
            return '\'' . pg_escape_string($value) . '\'';
        }
        elseif (is_bool($value))
        {
            return $value ? 'TRUE' : 'FALSE';
        }
        elseif (is_null($value))
        {
            return 'NULL';
        }
        elseif (is_nan($value))
        {
            return "'NaN'";
        }
        elseif (is_infinite($value))
        {
            return $value < 0 ? "'-Infinity'" : "'Infinity'";
        }
        elseif (is_int($value) OR is_float($value))
        {
            return $value;
        }
        else
        {
            throw new InvalidArgumentException('Unsupported argument type (' . gettype($value) . ') passed to ' . __METHOD__);
        }
    }

    /**
     *
     * @param resource $result - ресурс результата запроса к PostgreSQL
     * @param string $field_for_key - название поля, содержащего ключи итогового массива
     * @param string $field_for_value - название поля, содержащего значения итогового массива
     * @return array - массив вида "значение из поля $field_for_key" => "значение из поля $field_for_value"
     */
    public function fetchPairs($result, $field_for_key = 'id', $field_for_value = 'name')
    {
        $pairs = array();
        while ($row = pg_fetch_assoc($result))
        {
            $pairs[$row[$field_for_key]] = $row[$field_for_value];
        }
        return $pairs;
    }

}
