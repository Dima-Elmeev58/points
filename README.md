# points
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        // Чтение количества точек N из первой строки ввода
        int N = int.Parse(Console.ReadLine());

        // Создание списков для хранения точек в каждой из 4-х координатных четвертей
        List<(int x, int y)>[] pointsInQuadrant = new List<(int x, int y)>[4];
        for (int i = 0; i < 4; i++)
            pointsInQuadrant[i] = new List<(int x, int y)>();

        // Чтение координат N точек
        for (int i = 0; i < N; i++)
        {
            // Чтение строки с координатами и разделение на x и y
            string[] coords = Console.ReadLine().Split();
            int x = int.Parse(coords[0]);
            int y = int.Parse(coords[1]);

            // Пропуск точек, лежащих на осях (x=0 или y=0)
            if (x == 0 || y == 0)
                continue;

            // Распределение точек по четвертям:
            // 1-я четверть: x>0, y>0
            if (x > 0 && y > 0)
                pointsInQuadrant[0].Add((x, y));
            // 2-я четверть: x<0, y>0
            else if (x < 0 && y > 0)
                pointsInQuadrant[1].Add((x, y));
            // 3-я четверть: x<0, y<0
            else if (x < 0 && y < 0)
                pointsInQuadrant[2].Add((x, y));
            // 4-я четверть: x>0, y<0
            else if (x > 0 && y < 0)
                pointsInQuadrant[3].Add((x, y));
        }

        // Начальное предположение: 1-я четверть содержит максимальное количество точек
        int K = 1;
        int M = pointsInQuadrant[0].Count;

        // Поиск четверти с максимальным количеством точек
        for (int i = 1; i < 4; i++)
        {
            // Если найдена четверть с большим количеством точек
            if (pointsInQuadrant[i].Count > M)
            {
                K = i + 1;  // Нумерация четвертей с 1
                M = pointsInQuadrant[i].Count;
            }
            // Если количество точек одинаковое, выбираем по минимальному R
            else if (pointsInQuadrant[i].Count == M)
            {
                // Вычисление минимального R для текущей лучшей четверти
                int currentMinR = GetMinR(pointsInQuadrant[K - 1]);
                // Вычисление минимального R для рассматриваемой четверти
                int newMinR = GetMinR(pointsInQuadrant[i]);

                // Если R меньше или при равенстве номер четверти меньше
                if (newMinR < currentMinR || (newMinR == currentMinR && i + 1 < K))
                {
                    K = i + 1;
                    M = pointsInQuadrant[i].Count;
                }
            }
        }

        // Получение списка точек в выбранной четверти
        var selectedPoints = pointsInQuadrant[K - 1];
        // Начальное предположение: первая точка в списке
        (int x, int y) A = selectedPoints[0];
        // Вычисление R для первой точки (минимальное из |x| и |y|)
        int R = Math.Min(Math.Abs(A.x), Math.Abs(A.y));

        // Поиск точки с минимальным R в выбранной четверти
        foreach (var point in selectedPoints)
        {
            int currentR = Math.Min(Math.Abs(point.x), Math.Abs(point.y));
            if (currentR < R)
            {
                R = currentR;
                A = point;
            }
        }

        // Вывод результатов
        Console.WriteLine($"K = {K}");
        Console.WriteLine($"M = {M}");
        Console.WriteLine($"A = ({A.x}, {A.y})");
        Console.WriteLine($"R = {R}");
    }

    // Вспомогательная функция для нахождения минимального R в списке точек
    static int GetMinR(List<(int x, int y)> points)
    {
        // Если список пуст, возвращаем максимально возможное значение
        if (points.Count == 0)
            return int.MaxValue;

        // Начальное предположение: R первой точки
        int minR = Math.Min(Math.Abs(points[0].x), Math.Abs(points[0].y));

        // Поиск минимального R среди всех точек
        foreach (var point in points)
        {
            int currentR = Math.Min(Math.Abs(point.x), Math.Abs(point.y));
            if (currentR < minR)
                minR = currentR;
        }

        return minR;
    }
}
